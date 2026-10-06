# HTML to PDF conversion (Community Edition)

How to convert HTML to PDF with the free Community Edition (`Select.HtmlToPdf`), which performs HTML-to-PDF conversion only and caps the output at 5 pages per document.

## Converting a URL

The `HtmlToPdf` class converts an external URL (or a local HTML file on disk) to PDF through its `ConvertUrl(String)` method, which returns a `PdfDocument`. Set conversion options on `converter.Options` (an `HtmlToPdfOptions`), then call `PdfDocument.Save(string)` to write the file and `PdfDocument.Close()`. (An overload, `Save(Response, bool, fileName)`, streams the document straight to an ASP.NET `Response` — shown in the *Converting the current ASP.NET page* section below.)

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set converter options
converter.Options.PdfPageSize = pageSize;
converter.Options.PdfPageOrientation = pdfOrientation;
converter.Options.WebPageWidth = webPageWidth;
converter.Options.WebPageHeight = webPageHeight;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set converter options
converter.Options.PdfPageSize = pageSize
converter.Options.PdfPageOrientation = pdfOrientation
converter.Options.WebPageWidth = webPageWidth
converter.Options.WebPageHeight = webPageHeight

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Converting an HTML string

`HtmlToPdf` exposes two methods to convert a raw HTML string: `ConvertHtmlString(String)` and `ConvertHtmlString(String, String)`. The single-argument method is for HTML that has no external references to JavaScript, CSS files, or images. When the HTML references external resources using relative paths, use the `(html, baseUrl)` overload — the `baseUrl` parameter lets the converter resolve relative URLs (`baseUrl + relative image/css url = full absolute url`).

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// create a new pdf document converting an html string
PdfDocument doc = converter.ConvertHtmlString(htmlString, baseUrl);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' create a new pdf document converting an html string
Dim doc As PdfDocument = converter.ConvertHtmlString(htmlString, baseUrl)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Converting the current ASP.NET page

To convert the current ASP.NET WebForms page to PDF (preserving values entered into page controls before the convert button was clicked), override `Render(HtmlTextWriter)`, capture the page HTML into a `StringWriter` via `base.Render`, then feed `myWriter.ToString()` to `ConvertHtmlString` using `Request.Url.AbsoluteUri` as the base URL.

```csharp
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
Namespace SelectPdf.Samples
    Partial Public Class convert_current_page_to_pdf
        Inherits System.Web.UI.Page

        Private startConversion As Boolean = False

        Protected Sub BtnCreatePdf_Click(sender As Object, e As EventArgs)
            startConversion = True
        End Sub

        Protected Overrides Sub Render(writer As HtmlTextWriter)
            If startConversion Then
                ' get html of the page
                Dim myWriter As TextWriter = New StringWriter()
                Dim htmlWriter As New HtmlTextWriter(myWriter)
                MyBase.Render(htmlWriter)

                ' instantiate a html to pdf converter object
                Dim converter As New HtmlToPdf()

                ' create a new pdf document converting the html string of the page
                Dim doc As PdfDocument = converter.ConvertHtmlString(myWriter.ToString(), _
                                                                     Request.Url.AbsoluteUri)

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

## Page properties (size, orientation, margins)

`HtmlToPdfOptions.PdfPageSize` sets the page size (`PdfPageSize` enum: A0–A10, B0–B5, ArchA–ArchE, Letter, HalfLetter, Letter11x17, Note, Flsa, Ledger). `PdfPageOrientation` sets orientation (`PdfPageOrientation.Portrait` or `Landscape`). Margins are set with `MarginLeft`, `MarginRight`, `MarginTop`, and `MarginBottom`, specified in points (1 point = 1/72 inch). A custom page size can be set with `PdfPageCustomSize` instead of a standard `PdfPageSize`.

```csharp
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

## Browser viewport and auto-fit

The converter renders the page in an internal browser whose viewport is controlled by `WebPageWidth` (pixels, default 1024 — only a minimum; the converter auto-resizes wider if content does not fit, or auto-determines width when set to 0) and `WebPageHeight` (pixels, default 0 = auto-calculate full height; setting a value truncates content beyond it). Set `WebPageFixedSize` (default `false`) to `true` to force exactly `WebPageWidth` × `WebPageHeight` with no auto-resize — when fixed, a non-zero `WebPageHeight` must be supplied or the conversion fails. `WebPageWidth` and `WebPageHeight` can also be passed directly to the `HtmlToPdf` constructor. Content resizing into the PDF page is governed by `AutoFitWidth` and `AutoFitHeight`, both of type `HtmlToPdfPageFitMode` with values `NoAdjustment` (no resize; oversized content is cut), `ShrinkOnly` (shrink only when content exceeds the destination), and `AutoFit` (resize to fill the destination width). The default for `AutoFitWidth` is `ShrinkOnly`; the default for `AutoFitHeight` is `NoAdjustment` (and for height, `AutoFit` is treated as `ShrinkOnly`).

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set converter options
converter.Options.PdfPageSize = PdfPageSize.A4;
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait;

converter.Options.WebPageWidth = 1024;
converter.Options.WebPageHeight = 0;
converter.Options.WebPageFixedSize  = false;

converter.Options.AutoFitWidth = HtmlToPdfPageFitMode.ShrinkOnly;
converter.Options.AutoFitHeight = HtmlToPdfPageFitMode.NoAdjustment;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set converter options
converter.Options.PdfPageSize = PdfPageSize.A4
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait

converter.Options.WebPageWidth = 1024
converter.Options.WebPageHeight = 0
converter.Options.WebPageFixedSize  = False

converter.Options.AutoFitWidth = HtmlToPdfPageFitMode.ShrinkOnly
converter.Options.AutoFitHeight = HtmlToPdfPageFitMode.NoAdjustment

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)
```

## Resizing content during conversion

Because a web page is usually wider than a standard PDF page (e.g. an A4 page is 595 × 842 points ≈ 793px wide, while sites target 1024px+), content is either truncated or shrunk to fit. The combination of `WebPageWidth`/`WebPageHeight`/`WebPageFixedSize` (which size the internal browser) and `AutoFitWidth`/`AutoFitHeight` (which resize the rendered content into the PDF page) determines the result. The converter considers both `AutoFitWidth` and `AutoFitHeight` together when rendering. The minimal conversion below uses all the defaults described above.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)
```

## Conversion delay and timeout

For JavaScript-heavy pages, `MinPageLoadTime` (seconds) delays the conversion to let asynchronous content finish rendering. `MaxPageLoadTime` (seconds) is the web-page navigation timeout, used when a page takes too long to load. Both are properties of `HtmlToPdfOptions`.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// specify the number of seconds the conversion is delayed
converter.Options.MinPageLoadTime = 2;

// set the page timeout (in seconds)
converter.Options.MaxPageLoadTime = 30;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(TxtUrl.Text);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' specify the number of seconds the conversion is delayed
converter.Options.MinPageLoadTime = 2

' set the page timeout (in seconds)
converter.Options.MaxPageLoadTime = 30

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl.Text)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## CSS media type

Many pages define separate styles for `@media screen` and `@media print`. `HtmlToPdfOptions.CssMediaType` selects which set is used, of type `HtmlToPdfCssMediaType` with values `Screen` (default) and `Print`.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set css @media print
converter.Options.CssMediaType = HtmlToPdfCssMediaType.Print;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(TxtUrl.Text);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set css @media print
converter.Options.CssMediaType = HtmlToPdfCssMediaType.Print

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl.Text)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Page breaks

By default the converter automatically paginates content. `HtmlToPdfOptions.KeepTextsTogether` (default `True`) tries to avoid cutting text lines across PDF pages, and `KeepImagesTogether` (default `False`) tries to avoid cutting images. Pagination can also be controlled directly from CSS using `page-break-before: always`, `page-break-after: always`, and `page-break-inside: avoid` on any element (image, table, table row, div, text, etc.).

```csharp
converter.Options.KeepTextsTogether = true;
converter.Options.KeepImagesTogether = false;
```

```vbnet
converter.Options.KeepTextsTogether = True
converter.Options.KeepImagesTogether = False
```

CSS page-break markup in the HTML being converted:

```html
<div style="page-break-before: always">
A page break will be inserted BEFORE this div element.
</div>
<div style="page-break-after: always">
A page break will be inserted AFTER this div element.
</div>
<div style="page-break-inside: avoid">
The content of this div element will not be split into several pages (if possible).
</div>
```

## Repeat table headers

To repeat an HTML table's header at the top of every PDF page that the table spans, set the CSS property `display: table-header-group` on the table's `thead` element. This is configured entirely in the HTML/CSS, not via a converter option.

```html
<table>
  <thead style="display: table-header-group;">
   <tr>
      <th>Month</th>
      <th>Savings</th>
   </tr>
  </thead>
  <tbody>
   <tr>
      <td>January 2009</td>
      <td>$100</td>
   </tr>
   <!--// ..... many table rows that will split over several pages ...... -->
   <tr>
      <td>February 2015</td>
      <td>$200</td>
   </tr>
  </tbody>
</table>
```

## Keeping links

`HtmlToPdfOptions.InternalLinksEnabled` (default `True`) converts internal HTML links into internal PDF links, and `ExternalLinksEnabled` (default `True`) renders external hyperlinks in the PDF. Set either to `false` to disable that link type.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set links options
converter.Options.InternalLinksEnabled = true;
converter.Options.ExternalLinksEnabled = true;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(TxtUrl.Text);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set links options
converter.Options.InternalLinksEnabled = True
converter.Options.ExternalLinksEnabled = True

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl.Text)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Automatic bookmarks

The converter can generate PDF bookmarks from selected HTML elements. The selection is configured through `HtmlToPdfOptions.PdfBookmarkOptions` (an instance of `PdfBookmarkOptions`), whose single property `CssSelectors` takes a string array of CSS selectors — e.g. `"H1"` for all H1 elements, `"*.myclass"` for elements with class `myclass`, `"*#myid"` for the element with id `myid`. Set `ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines` to display the bookmarks when the document opens.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set the css selectors for the automatic bookmarks
converter.Options.PdfBookmarkOptions.CssSelectors = new string[] {"H1", "H2"};

// display the bookmarks when the document is opened in a viewer
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(TxtUrl.Text);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set the css selectors for the automatic bookmarks
converter.Options.PdfBookmarkOptions.CssSelectors = New String() {"H1", "H2"}

' display the bookmarks when the document is opened in a viewer
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl.Text)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Document properties

PDF document metadata is set through `PdfDocument.DocumentInformation`, which exposes `Title`, `Author`, `Subject`, `Keywords`, and `CreationDate`. After a conversion, `HtmlToPdf.ConversionResult` (an `HtmlToPdfResult`) exposes the source page's metadata via `WebPageInformation` (`Title`, `Description`, `Keywords`), so the PDF properties can be populated from the converted web page. (The option `HtmlToPdfOptions.PdfDocumentInformation` also references the generated document's information object.)

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(TxtUrl.Text);

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
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl.Text)

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

## Viewer preferences

`HtmlToPdfOptions.ViewerPreferences` (an `HtmlToPdfViewerPreferences`) controls how the PDF appears in a viewer. The boolean flags are `HideToolbar`, `HideMenuBar`, `HideWindowUI`, `FitWindow`, `CenterWindow`, and `DisplayDocTitle`. `PageMode` (`PdfViewerPageMode`: `UseNone`, `UseOutlines`, `UseThumbs`, `FullScreen`, `UseOC`, `UseAttachments`) sets the page mode at open time; `PageLayout` (`PdfViewerPageLayout`: `SinglePage`, `OneColumn`, `TwoColumnLeft`, `TwoColumnRight`) sets the layout; `NonFullScreenPageMode` (`PdfViewerFullScreenExitMode`: `UseNone`, `UseOutlines`, `UseThumbs`, `UseOC`) sets the mode used when the viewer exits full-screen.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set converter options
converter.Options.ViewerPreferences.CenterWindow = true;
converter.Options.ViewerPreferences.DisplayDocTitle = true;
converter.Options.ViewerPreferences.FitWindow = true;
converter.Options.ViewerPreferences.HideMenuBar = true;
converter.Options.ViewerPreferences.HideToolbar = true;
converter.Options.ViewerPreferences.HideWindowUI = true;

converter.Options.ViewerPreferences.PageLayout = PdfViewerPageLayout.SinglePage;
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseNone;
converter.Options.ViewerPreferences.NonFullScreenPageMode = PdfViewerFullScreenExitMode.UseNone;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");

// close pdf document
doc.Close();
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set converter options
converter.Options.ViewerPreferences.CenterWindow = True
converter.Options.ViewerPreferences.DisplayDocTitle = True
converter.Options.ViewerPreferences.FitWindow = True
converter.Options.ViewerPreferences.HideMenuBar = True
converter.Options.ViewerPreferences.HideToolbar = True
converter.Options.ViewerPreferences.HideWindowUI = True

converter.Options.ViewerPreferences.PageLayout = PdfViewerPageLayout.SinglePage
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseNone
converter.Options.ViewerPreferences.NonFullScreenPageMode = PdfViewerFullScreenExitMode.UseNone

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Other options review

Additional `HtmlToPdfOptions` members not covered above include: `EmbedFonts` (embed needed fonts), `JavaScriptEnabled` (run scripts when rendering), `PluginsEnabled` (enable plugins such as Flash players), `ColorSpace` (a `PdfColorSpace`), `PdfStandard` (a `PdfStandard`), `PdfCompressionLevel` (PDF compression level), `JpegCompressionEnabled` and `JpegCompressionLevel` (image JPEG compression), and `RenderingEngine` (selects the HTML rendering engine: `WebKit`, `WebKitRestricted`, `Blink`, or `Chromium`). `MaximumConcurrentConversions` must be set before the first conversion; a negative or zero value means maximum concurrency, the default is 4, and excess requests queue for an available slot. `ManagedContentTrimming` (default true): with the Chromium engine, the last page is cut where the content ends, so there is no trailing blank page; set it to false to keep the full last page. `ManagedContentTrimmingMode` chooses how the end is found: `Ink` (default) or `Marker`. `WebGlEnabled` (default false since v26.4): WebGL is off on the browser engines; set it to true only for pages that need WebGL. Note that header/footer (`Header`, `Footer`, `DisplayHeader`, `DisplayFooter`), authentication (`Authentication`), HTTP options (`HttpHeaders`, `HttpCookies`, `HttpPostParameters`), proxy (`ProxyOptions`), and security (`SecurityOptions`) are configured on the same options object.

See also: page-setup-headers-footers.md, auth-cookies-headers-post.md, installation-and-engines.md
