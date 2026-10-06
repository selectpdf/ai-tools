# HTML to PDF conversion

The commercial Select.Pdf .NET library converts URLs, local files, and raw HTML strings into PDF documents through the `HtmlToPdf` converter and its `HtmlToPdfOptions` settings.

## Converting a URL

The `HtmlToPdf` class converts a web page (an external URL or a local HTML file on disk) with the `ConvertUrl(String)` method, which returns a `PdfDocument`. Set options on `converter.Options` before converting, then save and `Close()` the document. The example also sets `RenderingEngine`, `PdfPageSize`, `PdfPageOrientation`, `WebPageWidth`, and `WebPageHeight`.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set converter options
converter.Options.RenderingEngine = RenderingEngine.Chromium;
converter.Options.PdfPageSize = PdfPageSize.A4;
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait;
converter.Options.WebPageWidth = 1024;
converter.Options.WebPageHeight = 0;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set converter options
converter.Options.RenderingEngine = RenderingEngine.Chromium
converter.Options.PdfPageSize = PdfPageSize.A4
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait
converter.Options.WebPageWidth = 1024
converter.Options.WebPageHeight = 0

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Converting an HTML string

To convert a raw HTML string, `HtmlToPdf` exposes `ConvertHtmlString(String)` and `ConvertHtmlString(String, String)`. Use the single-parameter form only when the HTML has no external references to JavaScript, CSS files, or images. When the HTML contains relative paths to external resources, use the two-parameter form: the second argument, `baseUrl`, lets the converter resolve relative URLs (`baseUrl + relative image/css url = full absolute url`). Select.Pdf supports HTML5 with CSS3 style sheets.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

string htmlString = "<html><body>Hello World from selectpdf.com.</body></html>";
string baseUrl = "https://selectpdf.com";

// create a new pdf document converting an html string
// (baseUrl resolves relative references to images/css/js)
PdfDocument doc = converter.ConvertHtmlString(htmlString, baseUrl);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

Dim htmlString As String = "<html><body>Hello World from selectpdf.com.</body></html>"
Dim baseUrl As String = "https://selectpdf.com"

' create a new pdf document converting an html string
' (baseUrl resolves relative references to images/css/js)
Dim doc As PdfDocument = converter.ConvertHtmlString(htmlString, baseUrl)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Converting multiple URLs into one document

To merge several conversions into a single PDF, convert each URL with `HtmlToPdf` to its own `PdfDocument`, then build a new `PdfDocument` and `Append` each result. With this approach each appended document begins on a new page. (An alternative approach uses `PdfHtmlElement` added to pages, which can continue successive conversions on the same page rather than always starting a new one.) Close the merged document and the individual source documents.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// convert individual urls
PdfDocument doc1 = converter.ConvertUrl(url1);
PdfDocument doc2 = converter.ConvertUrl(url2);
PdfDocument doc3 = converter.ConvertUrl(url3);

// create a new pdf document
PdfDocument doc = new PdfDocument();

// add the pages of those 3 documents to the new document
doc.Append(doc1);
doc.Append(doc2);
doc.Append(doc3);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();

// close the original pdf documents
doc1.Close();
doc2.Close();
doc3.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' convert individual urls
Dim doc1 As PdfDocument = converter.ConvertUrl(url1)
Dim doc2 As PdfDocument = converter.ConvertUrl(url2)
Dim doc3 As PdfDocument = converter.ConvertUrl(url3)

' create a new pdf document
Dim doc As New PdfDocument()

' add the pages of those 3 documents to the new document
doc.Append(doc1)
doc.Append(doc2)
doc.Append(doc3)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()

' close the original pdf documents
doc1.Close()
doc2.Close()
doc3.Close()
```

## Converting the current ASP.NET page

In ASP.NET Web Forms you can convert the page the user is currently viewing — including values entered into form controls — by overriding `Render`, capturing the page HTML into a `StringWriter`, and passing it to `ConvertHtmlString` with the request URL as `baseUrl`. A flag set in the button click handler decides whether to emit the PDF or render the page normally.

```csharp
using SelectPdf;

namespace SelectPdf.Samples
{
    public partial class convert_current_page_to_pdf : System.Web.UI.Page
    {
        private bool startConversion = false;

        protected void BtnCreatePdf_Click(object sender, EventArgs e)
        {
            startConversion = true;
        }

        protected override void Render(HtmlTextWriter writer)
        {
            if (startConversion)
            {
                // get html of the page
                TextWriter myWriter = new StringWriter();
                HtmlTextWriter htmlWriter = new HtmlTextWriter(myWriter);
                base.Render(htmlWriter);

                // instantiate a html to pdf converter object
                HtmlToPdf converter = new HtmlToPdf();

                // create a new pdf document converting the html string of the page
                PdfDocument doc = converter.ConvertHtmlString(
                    myWriter.ToString(), Request.Url.AbsoluteUri);

                // save pdf document
                doc.Save(Response, false, "Sample.pdf");

                // close pdf document
                doc.Close();
            }
            else
            {
                // render web page in browser
                base.Render(writer);
            }
        }
    }
}
```

```vbnet
Imports SelectPdf

Namespace SelectPdf.Samples
    Partial Public Class convert_current_page_to_pdf
        Inherits System.Web.UI.Page

        Private startConversion As Boolean = False

        Protected Sub BtnCreatePdf_Click(ByVal sender As Object, ByVal e As EventArgs)
            startConversion = True
        End Sub

        Protected Overrides Sub Render(ByVal writer As HtmlTextWriter)
            If startConversion Then
                ' get html of the page
                Dim myWriter As TextWriter = New StringWriter()
                Dim htmlWriter As New HtmlTextWriter(myWriter)
                MyBase.Render(htmlWriter)

                ' instantiate a html to pdf converter object
                Dim converter As New HtmlToPdf()

                ' create a new pdf document converting the html string of the page
                Dim doc As PdfDocument = converter.ConvertHtmlString( _
                    myWriter.ToString(), Request.Url.AbsoluteUri)

                ' save pdf document
                doc.Save(Response, False, "Sample.pdf")

                ' close pdf document
                doc.Close()
            Else
                ' render web page in browser
                MyBase.Render(writer)
            End If
        End Sub
    End Class
End Namespace
```

## Starting conversion from JavaScript

Select.Pdf exposes a global JavaScript variable `selectpdf` in the page being converted (`typeof(selectpdf) == "object"`; version via `selectpdf.version`). To trigger the conversion manually from page script, set `HtmlToPdfOptions.StartupMode` to `HtmlToPdfStartupMode.Manual` and call `selectpdf.start()` from JavaScript. If `StartupMode` is `Manual` and `selectpdf.start()` is never called, the conversion times out. The default `StartupMode` is `HtmlToPdfStartupMode.Automatic`, which starts conversion without waiting for any JavaScript call. Set `MaxPageLoadTime` to bound how long the converter waits.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// wait for selectpdf.start() to be called from the page's javascript
converter.Options.StartupMode = HtmlToPdfStartupMode.Manual;

// set timeout (in seconds)
converter.Options.MaxPageLoadTime = 10;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' wait for selectpdf.start() to be called from the page's javascript
converter.Options.StartupMode = HtmlToPdfStartupMode.Manual

' set timeout (in seconds)
converter.Options.MaxPageLoadTime = 10

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

In the page being converted, the script calls `selectpdf.start()` once it is ready (e.g. after asynchronous content has loaded):

```javascript
if (typeof(selectpdf) == "object") {
    selectpdf.start();
}
```

## Page settings (size, orientation, margins)

The PDF page geometry is controlled through `HtmlToPdfOptions`. Use `PdfPageSize` for a standard page size and `PdfPageOrientation` for orientation. Margins are set in points (1 point = 1/72 inch) via `MarginLeft`, `MarginRight`, `MarginTop`, and `MarginBottom`. Standard page sizes include A0–A10, B0–B5, ArchA–ArchE, and Letter, HalfLetter, Letter11x17, Note, Flsa, Ledger. Orientations are Portrait and Landscape. For a non-standard page, set a custom size with the `PdfPageCustomSize` property.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set converter options
converter.Options.PdfPageSize = PdfPageSize.A4;
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait;
converter.Options.MarginLeft = 10;
converter.Options.MarginRight = 10;
converter.Options.MarginTop = 20;
converter.Options.MarginBottom = 20;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set converter options
converter.Options.PdfPageSize = PdfPageSize.A4
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait
converter.Options.MarginLeft = 10
converter.Options.MarginRight = 10
converter.Options.MarginTop = 20
converter.Options.MarginBottom = 20

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Browser viewport & auto-fit

Select.Pdf renders the page in an internal browser whose viewport is controlled by `WebPageWidth` (pixels; default 1024px — a minimum recommendation, the converter enlarges it if content does not fit) and `WebPageHeight` (pixels; default 0px, meaning the converter auto-calculates the height so the whole page renders). Setting `WebPageWidth` to 0 lets the converter auto-determine the width. Setting `WebPageHeight` to a non-zero value truncates any content beyond it. `WebPageFixedSize` (default false) controls whether the internal browser uses a fixed size: when true, the page is rendered at the exact `WebPageWidth`/`WebPageHeight` even if content is truncated — and a non-zero `WebPageHeight` must then be set, because the default 0 makes the converter fail. Because web and PDF page widths differ, content fit is governed by `AutoFitWidth` and `AutoFitHeight`, both of type `HtmlToPdfPageFitMode` with values `NoAdjustment` (no resize; oversized content is cut), `ShrinkOnly` (shrink only when content exceeds the destination width/height), and `AutoFit` (resize content to fill the destination). The default for both is `ShrinkOnly`.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// render the page at a fixed browser viewport
converter.Options.WebPageWidth = 1024;
converter.Options.WebPageHeight = 1200;
converter.Options.WebPageFixedSize = true;

// control how content is resized to fit the pdf page
converter.Options.AutoFitWidth = HtmlToPdfPageFitMode.ShrinkOnly;
converter.Options.AutoFitHeight = HtmlToPdfPageFitMode.NoAdjustment;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' render the page at a fixed browser viewport
converter.Options.WebPageWidth = 1024
converter.Options.WebPageHeight = 1200
converter.Options.WebPageFixedSize = True

' control how content is resized to fit the pdf page
converter.Options.AutoFitWidth = HtmlToPdfPageFitMode.ShrinkOnly
converter.Options.AutoFitHeight = HtmlToPdfPageFitMode.NoAdjustment

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Resizing content during conversion

Resizing combines the PDF page size (`PdfPageSize`), the internal browser size (`WebPageWidth`, `WebPageHeight`, `WebPageFixedSize`), and the fit modes (`AutoFitWidth`, `AutoFitHeight` of type `HtmlToPdfPageFitMode`). Most sites are laid out for browser widths of 1024px or more, while an A4 page is ~793px wide, so wider content is shrunk to fit when `AutoFitWidth` is `ShrinkOnly` (the default). Choose `AutoFit` to scale content to exactly fill the destination width, or `NoAdjustment` to leave content unscaled (oversized content is cut off).

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

converter.Options.PdfPageSize = PdfPageSize.A4;
converter.Options.WebPageWidth = 1024;

// shrink wide content to fit the pdf page width, leave smaller content as-is
converter.Options.AutoFitWidth = HtmlToPdfPageFitMode.ShrinkOnly;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

converter.Options.PdfPageSize = PdfPageSize.A4
converter.Options.WebPageWidth = 1024

' shrink wide content to fit the pdf page width, leave smaller content as-is
converter.Options.AutoFitWidth = HtmlToPdfPageFitMode.ShrinkOnly

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Conversion delay & timeouts

For pages that rely heavily on JavaScript, delay the conversion a number of seconds with `MinPageLoadTime` (an additional time in seconds to wait for asynchronous items before rendering) so content can finish rendering. Bound how long the converter waits for the page to load with `MaxPageLoadTime` (the web page navigation timeout, in seconds). `MaxConversionTime` is also available on `HtmlToPdfOptions` to cap the overall conversion time.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// specify the number of seconds the conversion is delayed
converter.Options.MinPageLoadTime = 2;

// set the page timeout (in seconds)
converter.Options.MaxPageLoadTime = 30;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' specify the number of seconds the conversion is delayed
converter.Options.MinPageLoadTime = 2

' set the page timeout (in seconds)
converter.Options.MaxPageLoadTime = 30

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## CSS media type

Many pages define separate styles for screen (`@media screen`) and print (`@media print`). Choose which to apply with the `CssMediaType` property (type `HtmlToPdfCssMediaType`). The default is `Screen`; the other value is `Print`.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set css @media print
converter.Options.CssMediaType = HtmlToPdfCssMediaType.Print;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set css @media print
converter.Options.CssMediaType = HtmlToPdfCssMediaType.Print

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Partial page conversion

Convert only a specific section of a page. Set `VisibleWebElementId` to the HTML element's ID (the element can be an image, table, table row, div, text, etc.); it is honored by **every** rendering engine. With the **Blink** or **Chromium** engine you can instead set `VisibleWebElementSelector` to any CSS selector the engine understands (for example `"#content"`, `".infobox"` or `"table.wikitable tbody"`): the matched element is lifted out of the page and rendered on its own. A selector that matches nothing fails the conversion rather than converting the whole page. The WebKit engines cannot take a selector; use `VisibleWebElementId` with them. (Since v26.4, partial conversion produces PDF on the Blink and Chromium engines too; before, it applied only to WebKit / WebKit Restricted.)

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// convert only the element with this id (every engine)
converter.Options.VisibleWebElementId = elementId;

// or, with the Blink or Chromium engine, pick the element with a CSS selector:
// converter.Options.RenderingEngine = RenderingEngine.Chromium;
// converter.Options.VisibleWebElementSelector = "table.wikitable tbody";

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' convert only the element with this id (every engine)
converter.Options.VisibleWebElementId = elementId

' or, with the Blink or Chromium engine, pick the element with a CSS selector:
' converter.Options.RenderingEngine = RenderingEngine.Chromium
' converter.Options.VisibleWebElementSelector = "table.wikitable tbody"

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Excluding elements

Exclude elements from the output with `HiddenWebElements`, an instance of the `HiddenWebElements` class whose single property `CssSelectors` is a string array of CSS selectors. For example, `"img"` matches all images, `"*.myclass"` matches elements with class `myclass`, and `"*#myid"` matches the element with id `myid`.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// exclude all images
converter.Options.HiddenWebElements.CssSelectors = new string[] {"img"};

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' exclude all images
converter.Options.HiddenWebElements.CssSelectors = New String() {"img"}

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## PDF document properties

Set PDF metadata through `PdfDocument.DocumentInformation` — `Title`, `Author`, `Subject`, `Keywords`, and `CreationDate`. For HTML to PDF specifically, the converter can reuse the source page's own metadata: after conversion, `HtmlToPdf.ConversionResult` returns an `HtmlToPdfResult` whose `WebPageInformation` exposes the web page's `Title`, `Description`, and `Keywords`.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// get conversion result (contains document info from the web page)
HtmlToPdfResult result = converter.ConversionResult;

// set the document properties
doc.DocumentInformation.Title = result.WebPageInformation.Title;
doc.DocumentInformation.Subject = result.WebPageInformation.Description;
doc.DocumentInformation.Keywords = result.WebPageInformation.Keywords;

doc.DocumentInformation.Author = "Select.Pdf Samples";
doc.DocumentInformation.CreationDate = DateTime.Now;

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' get conversion result (contains document info from the web page)
Dim result As HtmlToPdfResult = converter.ConversionResult

' set the document properties
doc.DocumentInformation.Title = result.WebPageInformation.Title
doc.DocumentInformation.Subject = result.WebPageInformation.Description
doc.DocumentInformation.Keywords = result.WebPageInformation.Keywords

doc.DocumentInformation.Author = "Select.Pdf Samples"
doc.DocumentInformation.CreationDate = DateTime.Now

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Other options review

`HtmlToPdfOptions` (reached via `converter.Options`) and `HtmlToPdf` expose many more members. Notable ones from the options review:

- `RenderingEngine` — selects the HTML rendering engine: `WebKit`, `WebKitRestricted`, `Blink`, or `Chromium`. (Partial conversion: `VisibleWebElementId` works with every engine; `VisibleWebElementSelector` with Blink and Chromium.)
- `ManagedContentTrimming` (default true) — with the Chromium engine, the last page is cut where the content ends, on documents of any length, so there is no trailing blank page. Set it to false to keep the full last page. `ManagedContentTrimmingMode` chooses how the end of the content is found: `Ink` (default; measures the last painted content) or `Marker` (an invisible marker placed after the content).
- `WebGlEnabled` (default false since v26.4) — WebGL is off on the browser engines; set it to true only for pages that need WebGL. 2D canvas renders the same either way.
- `Header` / `Footer` and `DisplayHeader` / `DisplayFooter` (on `HtmlToPdf`) — custom header/footer content and visibility.
- `Authentication` — credentials for web pages that require authentication.
- `ProxyOptions` — proxy settings for fetching the page.
- `HttpHeaders`, `HttpCookies`, `HttpPostParameters` — custom HTTP headers, cookies, and POST parameters for the request.
- `JavaScriptEnabled` — enable or disable scripts when rendering. `PluginsEnabled` — enable plugins (e.g. Flash).
- `StartupScript` — JavaScript injected into the converted page; `StartupMode` — `Automatic` (default) or `Manual` (see "Starting conversion from JavaScript").
- `InternalLinksEnabled` / `ExternalLinksEnabled` — control rendering of internal and external hyperlinks.
- `KeepImagesTogether` / `KeepTextsTogether` — avoid splitting images / text lines across page breaks.
- `EmbedFonts` — embed needed fonts into the document.
- `ColorSpace` (type `PdfColorSpace`), `PdfCompressionLevel`, `JpegCompressionEnabled`, `JpegCompressionLevel` — color and compression of the output.
- `PdfStandard` (type `PdfStandard`) — generate PDF/A (including PDF/A-3) archival documents. `Tagged` — produce a tagged, accessible (PDF/UA) document with a structure tree; `Title` and `Language` record the tagged document's title and natural language.
- `MaximumConcurrentConversions` — set before the first conversion; default 4, with zero or negative meaning maximum concurrency (extra requests queue).
- `PdfBookmarkOptions`, `ViewerPreferences`, `SecurityOptions` — bookmarks, viewer preferences, and security settings.
- `WebElementsMappingOptions` — set its `CssSelectors` to retrieve the positions of HTML elements in the generated PDF (returned in the result object).

All four rendering engines (Blink/Chromium and WebKit/WebKit Restricted) are available; Chromium is the default used in the current samples.

See also: page-setup-headers-footers.md, links-bookmarks-toc.md, web-elements-and-auth.md, installation-and-engines.md
