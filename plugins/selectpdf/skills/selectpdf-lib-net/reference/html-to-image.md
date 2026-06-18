# HTML to image (render web pages to PNG/JPG)

Use the `HtmlToImage` class from the commercial Select.Pdf .NET library to render a web page (by URL) or a raw HTML string to a PNG, JPEG, or BMP image.

## Convert a URL to an image

Instantiate `HtmlToImage`, optionally set `RenderingEngine`, `WebPageWidth`, and `WebPageHeight`, then call `ConvertUrl(string url)`. The url can be an external address or a local html file on disk. `ConvertUrl` returns a `System.Drawing.Image`, which you save to a file in any format supported by `System.Drawing.Imaging.ImageFormat` (e.g. `ImageFormat.Png`, `ImageFormat.Jpeg`, `ImageFormat.Bmp`).

```csharp
using SelectPdf;
using System.Drawing;
using System.Drawing.Imaging;

// instantiate a html to image converter object
HtmlToImage imgConverter = new HtmlToImage();
imgConverter.RenderingEngine = RenderingEngine.Chromium;

// set converter options
imgConverter.WebPageWidth = 1024;
imgConverter.WebPageHeight = 0;

// create a new image converting an url
Image image = imgConverter.ConvertUrl("https://selectpdf.com");

// save the image to a file
image.Save("page.png", ImageFormat.Png);
```

```vbnet
Imports SelectPdf
Imports System.Drawing
Imports System.Drawing.Imaging

' instantiate a html to image converter object
Dim imgConverter As New HtmlToImage()
imgConverter.RenderingEngine = RenderingEngine.Chromium

' set converter options
imgConverter.WebPageWidth = 1024
imgConverter.WebPageHeight = 0

' create a new image converting an url
Dim image As Image = imgConverter.ConvertUrl("https://selectpdf.com")

' save the image to a file
image.Save("page.png", ImageFormat.Png)
```

## Convert an HTML string to an image

`HtmlToImage` exposes two overloads: `ConvertHtmlString(string htmlString)` and `ConvertHtmlString(string htmlString, string baseUrl)`. Use the single-parameter overload for html that has no external references to JavaScript, CSS files, or images. Use the two-parameter overload when the html references external resources through relative paths — `baseUrl` resolves those relative urls (baseUrl + relative image/css url = full absolute url). Both overloads return a `System.Drawing.Image` that you save to a file.

```csharp
using SelectPdf;
using System.Drawing;
using System.Drawing.Imaging;

string htmlString = "<html><body><b>Hello World from selectpdf.com.</b></body></html>";
string baseUrl = "https://selectpdf.com";

// instantiate a html to image converter object
HtmlToImage imgConverter = new HtmlToImage();
imgConverter.RenderingEngine = RenderingEngine.Chromium;

// set converter options
imgConverter.WebPageWidth = 1024;
imgConverter.WebPageHeight = 0;

// create a new image converting a raw html string
Image image = imgConverter.ConvertHtmlString(htmlString, baseUrl);

// save the image to a file
image.Save("page.png", ImageFormat.Png);
```

```vbnet
Imports SelectPdf
Imports System.Drawing
Imports System.Drawing.Imaging

Dim htmlString As String = "<html><body><b>Hello World from selectpdf.com.</b></body></html>"
Dim baseUrl As String = "https://selectpdf.com"

' instantiate a html to image converter object
Dim imgConverter As New HtmlToImage()
imgConverter.RenderingEngine = RenderingEngine.Chromium

' set converter options
imgConverter.WebPageWidth = 1024
imgConverter.WebPageHeight = 0

' create a new image converting a raw html string
Dim image As Image = imgConverter.ConvertHtmlString(htmlString, baseUrl)

' save the image to a file
image.Save("page.png", ImageFormat.Png)
```

See also: html-to-pdf.md, installation-and-engines.md
