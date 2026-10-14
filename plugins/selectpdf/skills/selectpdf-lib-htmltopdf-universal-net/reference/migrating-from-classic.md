# Migrating from the classic Community Edition

Converted from the shipped SelectPdf.HtmlToPdf.Universal (Community Edition) v26.5 help topics. C# and VB.NET samples are the official ones.

## Migrating to SelectPdf.HtmlToPdf.Universal

SelectPdf.HtmlToPdf.Universal is the cross-platform successor of the free **Select.HtmlToPdf** Community Edition. It keeps the class names, members and overloads of Select.HtmlToPdf wherever they could be kept, so most code moves over with mechanical changes: new packages, a new namespace, and SelectPdf's own drawing types in place of **System.Drawing**. This topic lists every change a project has to make, then the differences in behaviour worth knowing before the first release built on SelectPdf.HtmlToPdf.Universal.

### What Changes at a Glance

|  | Select.HtmlToPdf | SelectPdf.HtmlToPdf.Universal |
|---|---|---|
| Platforms | Windows | Windows (x64, x86), Linux (x64, ARM64), macOS (Apple Silicon), Docker containers |
| Frameworks | .NET Framework 2.0 and later, .NET Standard 2.0 | .NET Standard 2.0 (.NET Framework 4.6.1 and later, .NET Core 2.0 and later), .NET 8, .NET 10 |
| NuGet packages | **Select.HtmlToPdf**, **Select.HtmlToPdf.NetCore**, and engine companion packages | **SelectPdf.HtmlToPdf.Universal** plus one **SelectPdf.Universal.Native.<rid>** package per platform |
| Assembly | **Select.HtmlToPdf.dll** | **SelectPdf.HtmlToPdf.Universal.dll** |
| Namespaces | **SelectPdf** (with **System.Drawing** types in the API) | **SelectPdf.Universal** and **SelectPdf.Universal.Drawing** |
| HTML rendering engines | WebKit (the default), WebKit Restricted, Blink, Chromium | Chromium only |
| Engine files | **Select.Html.dep**, a Chromium or Blink folder | The **Chromium-CEF-154.0.28** folder, deployed by the native package |
| Page limit | 5 pages | 5 pages |

### Step 1: Replace the Packages

Remove the Select.HtmlToPdf package references (including the Chromium and Blink companion packages), any direct reference to **Select.HtmlToPdf.dll**, and the engine files a project carries by hand (**Select.Html.dep**, the engine folders and any build step that copies them). Then install **SelectPdf.HtmlToPdf.Universal** and the native package for each platform the application runs on:

```text
dotnet add package SelectPdf.HtmlToPdf.Universal
# the native package: win-x64, win-x86, linux-x64, linux-arm64 or osx-arm64
dotnet add package SelectPdf.Universal.Native.win-x64
```

The managed package is AnyCPU. Pick the native package that matches the architecture of the process that converts (**win-x86** for a 32-bit Windows process). The complete list of previous packages, download archives and leftover files is in the "Previous Community Edition" section of "Installation" (installation-and-deployment.md).

.NET Framework 2.0 to 4.5 are not supported; an application on one of them has to move to .NET Framework 4.6.1 or later, or to .NET.

### Step 2: Change the Namespace

Every public type lives in **SelectPdf.Universal**, and the drawing primitives in **SelectPdf.Universal.Drawing**. Replace the imports, and any fully qualified name:

| Select.HtmlToPdf | SelectPdf.HtmlToPdf.Universal |
|---|---|
| `using SelectPdf;` | `using SelectPdf.Universal;` |
| `Imports SelectPdf` | `Imports SelectPdf.Universal` |
| `SelectPdf.HtmlToPdf`, `SelectPdf.PdfDocument`, ... | `SelectPdf.Universal.HtmlToPdf`, `SelectPdf.Universal.PdfDocument`, ... |
| The assembly name `Select.HtmlToPdf` in configuration files, binding redirects or type names loaded by reflection | `SelectPdf.HtmlToPdf.Universal` |

The full library (**SelectPdf.Universal**) uses the same **SelectPdf.Universal** namespace, so moving up to it later needs no code change.

### Step 3: Replace System.Drawing Types

SelectPdf.HtmlToPdf.Universal does not use **System.Drawing**, which is Windows-only on current .NET. Every member that took or returned one of its types now uses a type of the library:

| Select.HtmlToPdf (System.Drawing) | SelectPdf.HtmlToPdf.Universal |
|---|---|
| `Color` | `PdfColor` (for example `new PdfColor(255, 0, 0)`), or `SelectPdf.Universal.Drawing.Color`, which converts to `PdfColor` implicitly |
| `PointF`, `RectangleF`, `SizeF` | `SelectPdf.Universal.Drawing.PointF`, `RectangleF`, `SizeF`, with the same members. Used by `PdfPageCustomSize`, the conversion result (`PdfPageLastRectangle`, `PdfPagesRectangles`, `WebPageSize`), destinations, templates and `MeasureString`. |
| `Font` (`PdfTextSection`, `PdfDocument.AddFont`, `PdfFontCollection.Add`) | `PdfFont` for text sections; `PdfSystemFont` to describe an installed font. See Fonts below. |
| `Image` (`PdfImageSection`) | A `Stream` with the encoded image, or a file path. `PdfImageSection.Image` is `PdfImageSection.ImageStream`. |

Before, with Select.HtmlToPdf:

```csharp
using System.Drawing;
using SelectPdf;

HtmlToPdf converter = new HtmlToPdf();
converter.Options.PdfPageCustomSize = new SizeF(600, 800);
converter.Options.DisplayFooter = true;
Font font = new Font("Arial", 8);
PdfTextSection pageNumber = new PdfTextSection(0, 10, "Page {page_number}", font);
pageNumber.ForeColor = Color.Gray;
converter.Footer.Add(pageNumber);
PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");
```

```vbnet
Imports System.Drawing
Imports SelectPdf

Dim converter As New HtmlToPdf()
converter.Options.PdfPageCustomSize = New SizeF(600, 800)
converter.Options.DisplayFooter = True
Dim font As New Font("Arial", 8)
Dim pageNumber As New PdfTextSection(0, 10, "Page {page_number}", font)
pageNumber.ForeColor = Color.Gray
converter.Footer.Add(pageNumber)
Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")
```

After, with SelectPdf.HtmlToPdf.Universal:

```csharp
using SelectPdf.Universal;
using SelectPdf.Universal.Drawing;

HtmlToPdf converter = new HtmlToPdf();
converter.Options.PdfPageSize = PdfPageSize.Custom;
converter.Options.PdfPageCustomSize = new SizeF(600, 800);
converter.Options.DisplayFooter = true;
PdfFont font = PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 8);
PdfTextSection pageNumber = new PdfTextSection(0, 10, "Page {page_number}", font);
pageNumber.ForeColor = new PdfColor(128, 128, 128);
converter.Footer.Add(pageNumber);
PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");
```

```vbnet
Imports SelectPdf.Universal
Imports SelectPdf.Universal.Drawing

Dim converter As New HtmlToPdf()
converter.Options.PdfPageSize = PdfPageSize.Custom
converter.Options.PdfPageCustomSize = New SizeF(600, 800)
converter.Options.DisplayFooter = True
Dim font As PdfFont = PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 8)
Dim pageNumber As New PdfTextSection(0, 10, "Page {page_number}", font)
pageNumber.ForeColor = New PdfColor(128, 128, 128)
converter.Footer.Add(pageNumber)
Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")
```

### Step 4: Move to the Chromium Engine

SelectPdf.HtmlToPdf.Universal renders HTML with one engine, Chromium. `RenderingEngine` is kept so existing code compiles; its values now say where Chromium runs - `RenderingEngine.Chromium` (the default) on the machine the application runs on, `RenderingEngine.ChromiumRemote` in a remote SelectPdf engine (see "The remote engine" (installation-and-deployment.md)). Assignments of `WebKit`, `WebKitRestricted` or `Blink` have to be removed. Select.HtmlToPdf used WebKit by default, so an application that never set the engine was rendering with WebKit and will see the output change: a current browser layout, different page breaks and page counts, and full support for modern CSS and JavaScript. Review the documents the application produces, and control pagination from the page's CSS (`break-before`, `break-after`, `break-inside: avoid`) - see "Page breaks" (html-to-pdf.md) and "The Chromium engine" (installation-and-deployment.md).

Settings that belonged to the other engines are gone:

| Select.HtmlToPdf | SelectPdf.HtmlToPdf.Universal |
|---|---|
| `CefEnginePath`, `CefEngineTemporaryFilesPath` | `ChromiumEnginePath`, `ChromiumEngineTemporaryFilesPath`, on the converter options and on `PdfHtmlSection`. Normally not needed: the engine is found next to the application. |
| `GlobalProperties.HtmlEngineFullPath` | `ChromiumEnginePath` on the object that converts |
| `GlobalProperties.PdfToolsFullPath`, `EnableRestrictedRenderingEngine`, `EnableFallbackToRestrictedRenderingEngine` | Removed |
| `BlinkEnginePath`, `BlinkEngineTemporaryFilesPath`, `BlinkEngineLaunchTimeout`, `BlinkEngineLaunchMaxTries`, `BlinkMaxThreadPoolWorkers`, `ExternalBrowserEndpoint` | Removed (no Blink engine). The number of conversions that run at once is `HtmlToPdfOptions.MaximumConcurrentConversions`. |
| `AutoFitWidth`, `AutoFitHeight` (`HtmlToPdfPageFitMode`) | Removed: WebKit scaling modes. Size the content with `WebPageWidth`, the page size and CSS - see "Resizing content during conversion" (html-to-pdf.md). |
| `KeepTextsTogether`, `KeepImagesTogether`, `PageBreaksEnhancedAlgorithm`, `DisplayCutText` | Removed: WebKit pagination settings that Chromium does not have. Use CSS `break-inside: avoid` on the elements that must not be split. |
| `EmbedFonts` | Removed: Chromium always embeds the fonts a page uses, as subsets. |
| `ScaleImages`, `PluginsEnabled`, `SecureProtocol` (and the `SecureProtocol` enumeration), `LFNB` | Removed: they had no effect on the Chromium engine. The log file is `LFN`. |

### Step 5: Renamed Members

Two settings have new names. The old names still compile, marked obsolete, and forward to the new ones: `MinPageLoadTime` is `ConversionDelay` (seconds to wait after the page loads) and `MaxPageLoadTime` is `NavigationTimeout` (seconds to wait for the page to load), on the converter options and on `PdfHtmlSection`. `CefEnginePath` and `CefEngineTemporaryFilesPath` were renamed without an alias (see Step 4). A few method parameters were renamed as well (`PdfDocument.RemovePage` and the `PdfPageCollection` methods); this only matters to calls that use named arguments.

### Step 6: Features Available Only in the Full Library

Select.HtmlToPdf exposed a few features that the free SelectPdf.HtmlToPdf.Universal does not. Code that uses them needs the full **SelectPdf.Universal** package (same namespace, same code):

- `HtmlToImage` - HTML to image conversion. In the full library it returns the image as a `byte[]` (PNG by default) instead of a `System.Drawing.Image`.
- `HiddenWebElements` on `PdfHtmlSection`, and `VisibleWebElementId`, `VisibleWebElementSelector`, `StartupMode` and `StartupScript` on `PdfHtmlSection`. In a header or footer, hide or show content with CSS in its html instead.
- The document open action types (`PdfAction`, `PdfActionGoTo`, `PdfActionJavaScript`, `PdfDocumentOpenAction`), which no Community member could use.
- `PdfStandard.PdfA3A`, which requires tagged output. PDF/A-1b, 2b, 3b, 3u, 4, 4e, 4f and PDF/X-1a are available.

Also removed: `PdfStandard.PdfSiqQLevelA` and `PdfSiqQLevelB`, `PdfViewerTextOrder`, the public constructors of `GlobalProperties` (now a static class) and `PdfFontCollection` (use `PdfDocument.Fonts`).

### Fonts

Fonts are where a Windows-only converter and a cross-platform one differ most, because the fonts installed on a Linux server or in a container are not the fonts of a Windows desktop.

**Porting font code**

| Select.HtmlToPdf | SelectPdf.HtmlToPdf.Universal |
|---|---|
| `doc.Fonts.Add(PdfStandardFont.Helvetica)`, then `font.Size = 18` | Unchanged. `PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 18)` is a one-line alternative. |
| `new PdfTextSection(x, y, text, new Font("Arial", 8))` | `new PdfTextSection(x, y, text, PdfFont.CreateFromSystemFont("Arial", 8))`, or a standard font |
| `doc.Fonts.Add(new Font("Arial", 12, FontStyle.Bold))` | `doc.Fonts.Add(new PdfSystemFont("Arial", 12, PdfFontStyle.Bold))`. Underline and strikeout are properties of the font: `IsUnderline`, `IsStrikeout`. |
| `doc.Fonts.Add(font, false)` (an installed font, not embedded) | Not supported: an installed font is always embedded, as a subset. For a font that is not embedded, use one of the 14 standard fonts. |
| `doc.Fonts.Add(@"C:\fonts\font.ttf")` | Unchanged, or `PdfFont.CreateFromFile(path, size)`; also from memory with `PdfFont.CreateFromBytes` and `PdfFont.CreateFromStream`. |
| `System.Drawing.FontFamily.Families` | `PdfSystemFonts.InstalledFamilies`, `PdfSystemFonts.IsFamilyInstalled` |

**How fonts are found on each platform**

- A font requested by family name (`PdfSystemFont`, `PdfFont.CreateFromSystemFont`) is looked up among the fonts installed on the machine the application runs on, by exact family name. A family that is not installed is an error, never a silent substitution. **Arial**, **Times New Roman** or **Segoe UI** are installed on Windows, but usually not on Linux servers or in containers.
- The library carries three font families of its own: **Liberation Sans**, **Liberation Serif** and **Liberation Mono**, with bold and italic faces, metric-compatible with Arial, Times New Roman and Courier New. They are available on every platform, including a container with no fonts installed. Code that must produce the same document everywhere should ask for these names.
- The 14 standard PDF fonts and the standard CJK fonts are not embedded: the PDF reader supplies them on every platform. In a PDF/A or PDF/X document, a standard font is drawn with the bundled face with the same metrics and embedded (Helvetica with Liberation Sans, Times with Liberation Serif, Courier with Liberation Mono); Symbol, ZapfDingbats and the standard CJK fonts cannot be used there.
- Chromium uses the fonts installed on the machine and the web fonts a page loads, and embeds every font the page uses, as a subset. On Linux, the library makes the generic families (`sans-serif`, `serif`, `monospace`) fall back to the bundled Liberation fonts when the system has nothing better, so text renders even in a container with no fonts at all. A page that names a font (`font-family: Arial`) renders with that font only where it is installed; to reproduce a Windows layout on Linux, install the fonts the pages use, or load them as web fonts.
- Windows containers based on Windows Server Core need the core Windows font families installed; see "Docker" (installation-and-deployment.md).
- Text drawn with a standard CJK font appears about one line lower than in Select.HtmlToPdf, which painted it one line above the requested position. Latin text and TrueType fonts are placed as before.

### Differences in Behaviour and Defaults

| Area | Select.HtmlToPdf | SelectPdf.HtmlToPdf.Universal |
|---|---|---|
| Where the Chromium engine runs | In a separate process for each conversion | Inside the application's process when the native package matches its architecture; otherwise in a separate process. `GlobalProperties.ChromiumEngineHostMode` chooses for the whole process and `ForceOutOfProcess` for one conversion. See "The Chromium engine" (installation-and-deployment.md). |
| Conversions running at once (`HtmlToPdfOptions.MaximumConcurrentConversions`) | 4 | 8 |
| HTTP cache between conversions | Not kept | Kept (`HttpCache = HttpCacheMode.All`). A service that converts pages for different users should set `HttpCacheMode.Clear`; see "Security recommendations" (troubleshooting-and-security.md). |
| Lazy image loading delay (`LazyImagesLoadingDelay`) | 50 ms | 40 ms |
| Encryption when a password is set | RC4 128-bit | AES 256-bit (revision 6). Readers older than Adobe Acrobat X cannot open it; set `KeySize` and `Algorithm` on `SecurityOptions` to AES 128-bit for them. See "Encrypting regulated data" (html-to-pdf.md). |
| Default passwords in `PdfSecurityOptions` | Empty strings | `null` (no password) |
| Default custom page size | 595 x 842 points | 595.28 x 841.89 points (exact A4) |
| Producer in the document properties | Select.Pdf Html To Pdf ... | SelectPdf Html To Pdf ... |
| `NetworkProxyType` values | Socks5 = 1 ... HttpCaching = 4 | Socks5 = 0 ... HttpCaching = 3. The names are the same; only code that stores the numbers is affected. |

Unchanged: the 5-page limit, the 28-point default page margins, JPEG compression (on, level 10), the navigation timeout (60 seconds), `WebGlEnabled` (false) and the default permissions.

### Deployment

- The native package deploys the engine files next to the application at build and publish time; there is nothing to copy by hand and no build step to add.
- On Linux, the native package brings the libraries the Chromium engine needs, so no system packages have to be installed; glibc 2.30 or later is required (Debian 11, Ubuntu 20.04, RHEL 9 and later). See "Deployment" (installation-and-deployment.md), "Docker" (installation-and-deployment.md) and "Microsoft Azure" (installation-and-deployment.md).
- A project references one native package. An application published for several platforms chooses its native package by runtime identifier and is published once per platform; see "Installation" (installation-and-deployment.md).

### New in SelectPdf.HtmlToPdf.Universal

- Asynchronous conversion with cancellation: `ConvertUrlAsync`, `ConvertHtmlStringAsync` - "Asynchronous conversion" (html-to-pdf.md).
- PDF/A-4, PDF/A-4e and PDF/A-4f output; AES-256 and AES-GCM encryption and unencrypted metadata.
- Fonts from memory and system font discovery (`PdfSystemFonts`), `PdfDocument.Append` from a file or a stream, and bookmark helpers.
- A remote engine that runs conversions in a container on another machine - "The remote engine" (installation-and-deployment.md).
- `HtmlToPdfResult.PdfPageCountBeforeLimit`, which tells when the 5-page limit cut a document - "The HTML to PDF converter" (html-to-pdf.md).
- `PdfDocument` implements `IDisposable`, so it can be used in a `using` statement.

### Checklist

1. Replace the packages, remove the old engine files and build steps (Step 1).
2. Replace `SelectPdf` with `SelectPdf.Universal` in imports and qualified names (Step 2).
3. Replace **System.Drawing** types (Step 3) and font code (Fonts).
4. Remove engine selection and the removed settings; rename `CefEnginePath` (Step 4).
5. If the application uses `HtmlToImage` or another feature from Step 6, move to the full **SelectPdf.Universal** package.
6. Review the behaviour table - in particular engine hosting, the HTTP cache in multi-user services, and the encryption default.
7. Compare the documents the application produces, especially if it rendered with WebKit, and test on every platform it deploys to.
