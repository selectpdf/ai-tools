# Migrating from the classic SelectPdf library

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## Migrating to SelectPdf.Universal

SelectPdf.Universal is the cross-platform successor of the **Select.Pdf** library. It keeps the class names, members and overloads of Select.Pdf wherever they could be kept, so most code moves over with mechanical changes: new packages, a new namespace, and SelectPdf's own drawing types in place of **System.Drawing**. This topic lists every change a project has to make, then the differences in behaviour worth knowing before the first release built on SelectPdf.Universal.

### What Changes at a Glance

|  | Select.Pdf | SelectPdf.Universal |
|---|---|---|
| Platforms | Windows | Windows (x64, x86), Linux (x64, ARM64), macOS (Apple Silicon), Docker containers |
| Frameworks | .NET Framework 2.0 and later, .NET Standard 2.0 | .NET Standard 2.0 (.NET Framework 4.6.1 and later, .NET Core 2.0 and later), .NET 8, .NET 10 |
| NuGet packages | **Select.Pdf**, **Select.Pdf.NetCore**, their x64 builds, and engine companion packages | **SelectPdf.Universal** plus one **SelectPdf.Universal.Native.<rid>** package per platform |
| Assembly | **Select.Pdf.dll** | **SelectPdf.Universal.dll** |
| Namespaces | **SelectPdf** (with **System.Drawing** types in the API) | **SelectPdf.Universal** and **SelectPdf.Universal.Drawing** |
| HTML rendering engines | WebKit (the default), WebKit Restricted, Blink, Chromium | Chromium only |
| Engine files | **Select.Html.dep**, **Select.Tools.dep**, a Chromium or Blink folder | The **Chromium-CEF-154.0.28** folder and **SelectPdf.Tools.dep** (**SelectPdf.Tools** on Linux and macOS), deployed by the native package |
| License key | `GlobalProperties.LicenseKey` | The same property and key format; keys issued for version 26 or later are accepted |

### Step 1: Replace the Packages

Remove the Select.Pdf package references (including the Chromium and Blink companion packages and **Select.Pdf.Extras**), any direct reference to **Select.Pdf.dll**, and the engine files a project carries by hand (**Select.Html.dep**, **Select.Tools.dep**, the engine folders and any build step that copies them). Then install **SelectPdf.Universal** and the native package for each platform the application runs on:

```text
dotnet add package SelectPdf.Universal
# the native package: win-x64, win-x86, linux-x64, linux-arm64 or osx-arm64
dotnet add package SelectPdf.Universal.Native.win-x64
```

The managed package is AnyCPU; there is no separate x64 build. Pick the native package that matches the architecture of the process that converts (**win-x86** for a 32-bit Windows process). The complete list of previous packages, download archives and leftover files is in the "Previous SelectPdf library" section of "Installation" (installation-and-deployment.md).

.NET Framework 2.0 to 4.5 are not supported; an application on one of them has to move to .NET Framework 4.6.1 or later, or to .NET.

### Step 2: Change the Namespace

Every public type lives in **SelectPdf.Universal**, and the drawing primitives in **SelectPdf.Universal.Drawing**. Replace the imports, and any fully qualified name:

| Select.Pdf | SelectPdf.Universal |
|---|---|
| `using SelectPdf;` | `using SelectPdf.Universal;` |
| `Imports SelectPdf` | `Imports SelectPdf.Universal` |
| `SelectPdf.HtmlToPdf`, `SelectPdf.PdfDocument`, ... | `SelectPdf.Universal.HtmlToPdf`, `SelectPdf.Universal.PdfDocument`, ... |
| The assembly name `Select.Pdf` in configuration files, binding redirects or type names loaded by reflection | `SelectPdf.Universal` |

Because the namespaces differ, a project can reference Select.Pdf and SelectPdf.Universal side by side while it is ported one file at a time. The free Community Edition (**SelectPdf.HtmlToPdf.Universal**) uses the same **SelectPdf.Universal** namespace, so switching between the two editions needs no code change.

### Step 3: Replace System.Drawing Types

SelectPdf.Universal does not use **System.Drawing**, which is Windows-only on current .NET. Every member that took or returned one of its types now uses a type of the library:

| Select.Pdf (System.Drawing) | SelectPdf.Universal |
|---|---|
| `Color` | `PdfColor` (for example `new PdfColor(255, 0, 0)`), or `SelectPdf.Universal.Drawing.Color`, which converts to `PdfColor` implicitly and offers `FromArgb` and the common named colors |
| `PointF`, `RectangleF`, `SizeF` | `SelectPdf.Universal.Drawing.PointF`, `RectangleF`, `SizeF`, with the same members (`X`, `Width`, `Bottom`, `Contains`, `Empty`, ...). Used by link and annotation rectangles, destinations, `PdfPageLastRectangle`, `ClientRectangle`, `MeasureString`, templates and the signing overloads. |
| `Font` | `PdfFont` for text elements and sections; `PdfSystemFont` to describe an installed font. See Fonts below. |
| `Image` as input (`PdfImageElement`, `PdfImageSection`) | A `Stream` or a `byte[]` with the encoded image, or a file path. PNG, JPEG, GIF, BMP, WebP and TIFF are read. `PdfImageSection.Image` is `PdfImageSection.ImageStream`. |
| `Image` as output (`HtmlToImage.ConvertUrl`, `ConvertHtmlString`) | `byte[]`, encoded in `HtmlToImage.ImageFormat` (PNG by default) |
| `Image[]` from `PdfRasterizer.ConvertToImages` | `byte[][]`, one encoded image per page |
| `Imaging.ImageFormat` (`PdfRasterizer.ImagesFormat`) | `PdfImageFormat` |

Before, with Select.Pdf:

```csharp
using System.Drawing;
using SelectPdf;

PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();
PdfFont font = doc.AddFont(new Font("Arial", 12));
page.Add(new PdfTextElement(0, 0, "Hello", font, Color.DarkBlue));
PdfRectangleElement box = new PdfRectangleElement(new RectangleF(0, 30, 200, 20));
box.BackColor = Color.LightGray;
page.Add(box);
page.Add(new PdfImageElement(0, 60, Image.FromFile("logo.png")));

HtmlToImage imgConverter = new HtmlToImage();
Image pageImage = imgConverter.ConvertUrl("https://selectpdf.com");
pageImage.Save("page.png");
```

```vbnet
Imports System.Drawing
Imports SelectPdf

Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()
Dim font As PdfFont = doc.AddFont(New Font("Arial", 12))
page.Add(New PdfTextElement(0, 0, "Hello", font, Color.DarkBlue))
Dim box As New PdfRectangleElement(New RectangleF(0, 30, 200, 20))
box.BackColor = Color.LightGray
page.Add(box)
page.Add(New PdfImageElement(0, 60, Image.FromFile("logo.png")))

Dim imgConverter As New HtmlToImage()
Dim pageImage As Image = imgConverter.ConvertUrl("https://selectpdf.com")
pageImage.Save("page.png")
```

After, with SelectPdf.Universal:

```csharp
using System.IO;
using SelectPdf.Universal;
using SelectPdf.Universal.Drawing;

PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();
// Liberation Sans ships with the library; "Arial" works where it is installed
PdfFont font = PdfFont.CreateFromSystemFont("Liberation Sans", 12);
page.Add(new PdfTextElement(0, 0, "Hello", font, new PdfColor(0, 0, 139)));
PdfRectangleElement box = new PdfRectangleElement(new RectangleF(0, 30, 200, 20));
box.BackColor = new PdfColor(211, 211, 211);
page.Add(box);
page.Add(new PdfImageElement(0, 60, "logo.png"));

HtmlToImage imgConverter = new HtmlToImage();
byte[] png = imgConverter.ConvertUrl("https://selectpdf.com");
File.WriteAllBytes("page.png", png);
```

```vbnet
Imports System.IO
Imports SelectPdf.Universal
Imports SelectPdf.Universal.Drawing

Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()
' Liberation Sans ships with the library; "Arial" works where it is installed
Dim font As PdfFont = PdfFont.CreateFromSystemFont("Liberation Sans", 12)
page.Add(New PdfTextElement(0, 0, "Hello", font, New PdfColor(0, 0, 139)))
Dim box As New PdfRectangleElement(New RectangleF(0, 30, 200, 20))
box.BackColor = New PdfColor(211, 211, 211)
page.Add(box)
page.Add(New PdfImageElement(0, 60, "logo.png"))

Dim imgConverter As New HtmlToImage()
Dim png As Byte() = imgConverter.ConvertUrl("https://selectpdf.com")
File.WriteAllBytes("page.png", png)
```

### Step 4: Move to the Chromium Engine

SelectPdf.Universal renders HTML with one engine, Chromium. `RenderingEngine` is kept so existing code compiles; its values now say where Chromium runs - `RenderingEngine.Chromium` (the default) on the machine the application runs on, `RenderingEngine.ChromiumRemote` in a remote SelectPdf engine (see "The remote engine" (installation-and-deployment.md)). Assignments of `WebKit`, `WebKitRestricted` or `Blink` have to be removed. Select.Pdf used WebKit by default, so an application that never set the engine was rendering with WebKit and will see the output change: a current browser layout, different page breaks and page counts, and full support for modern CSS and JavaScript. Review the documents the application produces, and control pagination from the page's CSS (`break-before`, `break-after`, `break-inside: avoid`) - see "Page breaks" (html-to-pdf.md) and "The Chromium engine" (installation-and-deployment.md).

Settings that belonged to the other engines are gone:

| Select.Pdf | SelectPdf.Universal |
|---|---|
| `CefEnginePath`, `CefEngineTemporaryFilesPath` | `ChromiumEnginePath`, `ChromiumEngineTemporaryFilesPath`, on the converter options and on every html element, section and image converter. Normally not needed: the engine is found next to the application. |
| `GlobalProperties.HtmlEngineFullPath` | `ChromiumEnginePath` on the object that converts |
| `GlobalProperties.PdfToolsFullPath` | No setting: the PDF tool is deployed by the native package next to the application and found there |
| `GlobalProperties.EnableRestrictedRenderingEngine`, `EnableFallbackToRestrictedRenderingEngine` | Removed (no WebKit Restricted engine) |
| `BlinkEnginePath`, `BlinkEngineTemporaryFilesPath`, `BlinkEngineLaunchTimeout`, `BlinkEngineLaunchMaxTries`, `BlinkMaxThreadPoolWorkers`, `ExternalBrowserEndpoint` | Removed (no Blink engine). The number of conversions that run at once is `HtmlToPdfOptions.MaximumConcurrentConversions`. |
| `AutoFitWidth`, `AutoFitHeight` (`HtmlToPdfPageFitMode`) | Removed: WebKit scaling modes. Size the content with `WebPageWidth`, the page size and CSS - see "Resizing content during conversion" (html-to-pdf.md). |
| `KeepTextsTogether`, `KeepImagesTogether`, `PageBreaksEnhancedAlgorithm`, `DisplayCutText` | Removed: WebKit pagination settings that Chromium does not have. Use CSS `break-inside: avoid` on the elements that must not be split. |
| `EmbedFonts` | Removed: Chromium always embeds the fonts a page uses, as subsets. |
| `ScaleImages`, `PluginsEnabled`, `SecureProtocol` (and the `SecureProtocol` enumeration) | Removed: they had no effect on the Chromium engine. Chromium negotiates the TLS version itself. |
| `HtmlToPdfOptions.DemoMode`, `LFNB` | Removed. Evaluation mode follows the license key only; the log file is `LFN`. |

### Step 5: Renamed Members

Three settings have new names. The old names still compile, marked obsolete, and forward to the new ones, so the change can be made at any time:

| Select.Pdf | SelectPdf.Universal |
|---|---|
| `MinPageLoadTime` | `ConversionDelay` - seconds to wait after the page loads, before it is rendered |
| `MaxPageLoadTime` | `NavigationTimeout` - seconds to wait for the page to load |
| `VisibleWebElementId` | `VisibleWebElementSelector`, which takes any CSS selector; an id `x` is the selector `#x` |

`CefEnginePath` and `CefEngineTemporaryFilesPath` were renamed without an obsolete alias (see Step 4). A few method parameters were renamed as well (`PdfDocument.RemovePage` and the `PdfPageCollection` methods); this only matters to calls that use named arguments.

### Step 6: Features That Are Not Available

| Select.Pdf | SelectPdf.Universal |
|---|---|
| `PdfMergeManager` | `PdfDocument.Append(PdfDocument)`, which also takes a file name or a stream. For PDF/A output, create the document with `new PdfDocument(PdfStandard.PdfA2B)` (or another level) and append to it. See "Merging PDFs" (edit-merge-split-stamp.md). |
| `PdfPrinter` and its events, event arguments, `PdfPrinterException`, `PdfPrinterPageOrientation`, `PdfPrinterPageSizing` | Not available: printing relied on Windows-only APIs. Render the pages with `PdfRasterizer` and print them with the platform's printing API, or send the PDF to a print service. |
| `PdfManager` (the base class of the managers) | Removed. `PdfFormManager`, `PdfPortfolioManager`, `PdfResizeManager` and `PdfSecurityManager` keep their own `Load`, `Save`, `GetDocument` and `Close`. |
| **Select.Pdf.Extras** (its forms manager and compressor) | No add-on. Use `PdfFormManager` and the compression settings of `PdfDocument` - see "Form filling" (forms.md) and "PDF compression" (edit-merge-split-stamp.md). |
| `PdfStandard.PdfSiqQLevelA`, `PdfSiqQLevelB` | Not available. The PDF/A levels (1b to 4f) and PDF/X-1a are. |
| `PdfHtmlElement.DisplayHeaderOnOddPages`, `DisplayHeaderOnEvenPages`, `DisplayFooterOnOddPages`, `DisplayFooterOnEvenPages` | Removed: an html element has no header or footer of its own. Put bands on the document ("Templates" (pdf-creation-elements.md)). |
| `PdfViewerTextOrder` | Removed; no member used it. |
| The public constructors of `GlobalProperties`, `PdfFontCollection` and `PdfFormFieldsCollection`; `PdfTool.ParseDocInfo`; `virtual` on the `PdfResizeManager` methods | `GlobalProperties` is a static class; the collections are reached through `PdfDocument.Fonts` and `PdfFormManager.Fields`. |

### Fonts

Fonts are where a Windows-only library and a cross-platform one differ most, because the fonts installed on a Linux server or in a container are not the fonts of a Windows desktop.

**Porting font code**

| Select.Pdf | SelectPdf.Universal |
|---|---|
| `doc.Fonts.Add(PdfStandardFont.Helvetica)`, then `font.Size = 18` | Unchanged. `PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 18)` is a one-line alternative. |
| `doc.Fonts.Add(new Font("Arial", 12))`, `doc.AddFont(new Font(...))` | `doc.Fonts.Add(new PdfSystemFont("Arial", 12))`, or `PdfFont.CreateFromSystemFont("Arial", 12)` |
| `new Font("Arial", 12, FontStyle.Bold \| FontStyle.Italic)` | `new PdfSystemFont("Arial", 12, PdfFontStyle.Bold \| PdfFontStyle.Italic)`. Underline and strikeout are properties of the font: `IsUnderline`, `IsStrikeout`. |
| `doc.Fonts.Add(font, false)` (an installed font, not embedded) | Not supported: an installed font is always embedded, as a subset. For a font that is not embedded, use one of the 14 standard fonts, which every PDF reader has. |
| `doc.Fonts.Add(@"C:\fonts\font.ttf")` | Unchanged, or `PdfFont.CreateFromFile(path, size)`. Fonts can also be loaded from memory: `PdfFont.CreateFromBytes`, `PdfFont.CreateFromStream`, `doc.Fonts.Add(byte[])`, `doc.Fonts.Add(Stream)`. |
| `font.GetSystemFont()` (a `System.Drawing.Font`) | `font.GetSystemFont()` returns the `PdfSystemFont` the font was created from, or null |
| `System.Drawing.FontFamily.Families` | `PdfSystemFonts.InstalledFamilies`, `PdfSystemFonts.IsFamilyInstalled`, `PdfSystemFonts.LoadFontBytes` |
| `PdfTextSection(x, y, text, System.Drawing.Font)` | `PdfTextSection(x, y, text, PdfFont)` |

**How installed fonts are found on each platform**

- A font requested by family name (`PdfSystemFont`, `PdfFont.CreateFromSystemFont`) is looked up among the fonts installed on the machine the application runs on - the Windows font folder, the font directories of a Linux distribution, the macOS system fonts - by exact family name. A family that is not installed is an error (`PdfDocumentException`), never a silent substitution with another face. **Arial**, **Times New Roman**, **Verdana** or **Segoe UI** are installed on Windows, but usually not on Linux servers or in containers.
- The library carries three font families of its own, embedded in the assembly: **Liberation Sans**, **Liberation Serif** and **Liberation Mono**, each with bold and italic faces. They have the same character widths as Arial, Times New Roman and Courier New, so text laid out with them takes the same space. They are available on every platform, including a container with no fonts installed, and are always listed by `PdfSystemFonts.InstalledFamilies`. Code that must produce the same document on Windows, Linux and macOS should ask for these names instead of the Windows ones.
- The 14 standard PDF fonts (Helvetica, Times, Courier, Symbol, ZapfDingbats and their styles) and the standard CJK fonts are not embedded: the PDF reader supplies them, so they work the same on every platform.
- For an application running on Windows, nothing changes: the installed fonts are available as before.

**Fonts in HTML conversion**

- Chromium uses the fonts installed on the machine and the web fonts a page loads (`@font-face`, Google Fonts), and embeds every font the page uses, as a subset. Web fonts render the same everywhere; a page that relies on a font by name (`font-family: Arial`) renders with that font only where it is installed, and with another face elsewhere.
- On Linux, the library configures Chromium so that the generic families (`sans-serif`, `serif`, `monospace`) fall back to the bundled Liberation fonts when the system has nothing better. Text therefore renders even in a container with no fonts at all; a system that has fonts installed keeps its own. To reproduce a Windows layout exactly on Linux, install the fonts the pages use, or load them as web fonts.
- Windows containers based on Windows Server Core need the core Windows font families installed; see "Docker" (installation-and-deployment.md).

**Fonts in documents with a standard**

- PDF/A and PDF/X require every font to be embedded. A standard font used in such a document is drawn with the bundled face that has the same metrics, and embedded: Helvetica with Liberation Sans, Times with Liberation Serif, Courier with Liberation Mono. Symbol and ZapfDingbats have no bundled equivalent and are refused.
- The standard CJK fonts are never embedded, so they cannot be used in PDF/A or PDF/X documents: load a CJK TrueType or OpenType font with `PdfFont.CreateFromFile` instead.
- `PdfDocument.ConvertToPdfA` embeds the fonts a loaded document uses but does not carry, resolving each by name on the machine and falling back to the bundled family closest to it.

**Other differences**

- Text drawn with a standard CJK font appears about one line lower than in Select.Pdf, which painted CJK text one line above the requested position while reporting a rectangle below it. SelectPdf.Universal paints the text at the requested position, and the rectangle it reports matches. Latin text and TrueType fonts are placed as before.
- `Size`, `IsUnderline` and `IsStrikeout` can be changed on every kind of font after it is created, as in Select.Pdf.
- In the Word to PDF converter, a font a document names but the machine lacks is replaced by the closest bundled Liberation family.

### Differences in Behaviour and Defaults

| Area | Select.Pdf | SelectPdf.Universal |
|---|---|---|
| Where the Chromium engine runs | In a separate process for each conversion | Inside the application's process when the native package matches its architecture, which removes the start-up cost of each conversion; otherwise in a separate process. `GlobalProperties.ChromiumEngineHostMode` chooses for the whole process and `ForceOutOfProcess` for one conversion. See "The Chromium engine" (installation-and-deployment.md). |
| Conversions running at once (`HtmlToPdfOptions.MaximumConcurrentConversions`) | 4 | 8 |
| HTTP cache between conversions | Not kept | Kept (`HttpCache = HttpCacheMode.All`), which makes repeated conversions of the same sites faster. A service that converts pages for different users should set `HttpCacheMode.Clear`; see "Security recommendations" (troubleshooting-and-security.md). |
| Lazy image loading delay (`LazyImagesLoadingDelay`) | 50 ms | 40 ms |
| Encryption when a password is set | RC4 128-bit | AES 256-bit (revision 6). Readers older than Adobe Acrobat X cannot open it; set `KeySize` and `Algorithm` to AES 128-bit for them. See "Encrypting regulated data" (security-and-signatures.md). |
| Default passwords in `PdfSecurityOptions` | Empty strings | `null` (no password) |
| Language of tagged output | `PdfDocument.Language` "en-US", `HtmlToPdfOptions.Language` not set | `HtmlToPdfOptions.Language` "en-US", `PdfDocument.Language` not set; set it for the documents you tag |
| Default custom page size | 595 x 842 points | 595.28 x 841.89 points (exact A4) |
| Producer in the document properties | Select.Pdf for .NET v<version> | SelectPdf for .NET v<version> |
| `NetworkProxyType` values | Socks5 = 1 ... HttpCaching = 4 | Socks5 = 0 ... HttpCaching = 3. The names are the same; only code that stores the numbers is affected. |
| License errors | `HtmlToPdfException` for an expired license, a general exception for an invalid key | `LicenseException` for every license error |
| Reading a manager's properties before `Load` | `NullReferenceException` | `PdfDocumentException` with a message |

Unchanged: the 28-point default page margins, JPEG compression (on, level 10), stream compression (`Normal`), the navigation timeout (60 seconds), `WebGlEnabled` (false), the default permissions, the evaluation watermark text and the 5-page limit of the Community Edition.

### Deployment

- The native package deploys the engine files next to the application at build and publish time; there is nothing to copy by hand and no build step to add.
- On Linux, the native package brings the libraries the Chromium engine needs, so no system packages have to be installed; glibc 2.30 or later is required (Debian 11, Ubuntu 20.04, RHEL 9 and later). See "Deployment" (installation-and-deployment.md), "Docker" (installation-and-deployment.md) and "Microsoft Azure" (installation-and-deployment.md).
- A project references one native package. An application published for several platforms chooses its native package by runtime identifier and is published once per platform; see "Installation" (installation-and-deployment.md).

### New in SelectPdf.Universal

- Asynchronous conversion with cancellation: `ConvertUrlAsync`, `ConvertHtmlStringAsync`, `PdfCanvas.AddAsync` - "Asynchronous conversion" (html-to-pdf.md).
- Word to PDF: `WordToPdf`, `PdfWordElement`, `PdfWordSection` - "The Word to PDF converter" (word-to-pdf.md).
- Redaction that removes content from the file - "Redaction" (redaction.md).
- Signature validation, AES-GCM encryption and unencrypted metadata - "Digital signatures and signature validation" (security-and-signatures.md), "Advanced security" (security-and-signatures.md).
- PDF/A-4, PDF/A-4e, PDF/A-4f, PDF/UA-1 and PDF/UA-2 - "Accessible PDF and PDF/A" (accessibility-and-compliance.md); ZUGFeRD / Factur-X invoices - "ZUGFeRD / Factur-X electronic invoices" (accessibility-and-compliance.md).
- `HtmlToImageElement`, fonts from memory and system font discovery, `PdfDocument.Append` from a file or a stream, and position overloads (`x, y, width, height`) on link, annotation and signature elements.
- A remote engine that runs conversions in a container on another machine - "The remote engine" (installation-and-deployment.md).
- A table of contents with page numbers - "Table of contents" (links-bookmarks-toc.md); HTML forms converted into fillable PDF fields - "HTML forms to PDF form fields" (html-to-pdf.md).
- Signing with keys in an HSM, a smart card, a USB token or a key service, and long-term validation of signatures - "Digital signatures and signature validation" (security-and-signatures.md).
- `PdfDocument`, the managers and the PDF to text and PDF to image converters implement `IDisposable`, so they can be used in a `using` statement.

### Checklist

1. Replace the packages, remove the old engine files and build steps (Step 1).
2. Replace `SelectPdf` with `SelectPdf.Universal` in imports and qualified names (Step 2).
3. Replace **System.Drawing** types (Step 3) and font code (Fonts).
4. Remove engine selection and the removed settings; rename `CefEnginePath` (Step 4).
5. Optionally rename `MinPageLoadTime`, `MaxPageLoadTime` and `VisibleWebElementId` (Step 5).
6. Replace `PdfMergeManager` and any use of printing or the Extras add-on (Step 6).
7. Review the behaviour table - in particular engine hosting, the HTTP cache in multi-user services, and the encryption default.
8. Compare the documents the application produces, especially if it rendered with WebKit, and test on every platform it deploys to.
