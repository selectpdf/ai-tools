# HTML to PDF conversion

Converted from the shipped SelectPdf.HtmlToPdf.Universal (Community Edition) v26.4 help topics. C# and VB.NET samples are the official ones.

## The HTML to PDF converter

SelectPdf Html To Pdf Converter - Community Edition provides a powerful and easy to use free html to pdf converter that can be used in any .NET applications to generate pdf documents from web pages and html code.

SelectPdf Html To Pdf Converter - Community Edition provides most of the features of the Professional Select.Pdf Library for .NET. The main limitation is related to the number of pages of the generated pdf documents. The community edition generates pdf documents **up to 5 pages**.

### Quick Start

Using the Html to Pdf Converter is very easy. The first thing that needs to be done is the namespace importing.

```csharp
			using SelectPdf.Universal;
```

```vbnet
			Imports SelectPdf.Universal
```

Sample code that shows how to convert an url to pdf using Free Select.Pdf Html To Pdf Converter for .NET:

```csharp
			// instantiate a html to pdf converter object
            HtmlToPdf converter = new HtmlToPdf();

            // create a new pdf document converting an url
            PdfDocument doc = converter.ConvertUrl(url);

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' instantiate a html to pdf converter object
            Dim converter As New HtmlToPdf()

            ' create a new pdf document converting an url
            Dim doc As PdfDocument = converter.ConvertUrl(url)

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

Sample code that shows how to convert raw html code to pdf using Free Select.Pdf Html To Pdf Converter for .NET:

```csharp
			// instantiate a html to pdf converter object
            HtmlToPdf converter = new HtmlToPdf();

            // create a new pdf document converting the html code
            PdfDocument doc = converter.ConvertHtmlString(htmlString, baseUrl);

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' instantiate a html to pdf converter object
            Dim converter As New HtmlToPdf()

            ' create a new pdf document converting the html code
            Dim doc As PdfDocument = converter.ConvertHtmlString(htmlString, baseUrl)

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

### Html to Pdf Converter Properties

`HtmlToPdf.Options` - controls the html to pdf conversion process and the generated pdf document properties.

`HtmlToPdf.Header` - controls the properties of the custom header of the generated pdf document.

`HtmlToPdf.Footer` - controls the properties of the custom footer of the generated pdf document.

`HtmlToPdf.ConversionResult` - offers information about the result of the html to pdf conversion process.

These properties will be described in details in the next sections of the Developers Guide.

## Converting a URL

Using Select.Pdf Html to Pdf Converter for .NET any web page can be converted to pdf. The web page can be specified by an external url or can be a local html file on the disk.

To convert an url (or local file) to pdf, `HtmlToPdf` class exposes the `HtmlToPdf.ConvertUrl(string)` method.

### Sample Code

This sample code shows how to use Select.Pdf html to pdf converter to convert an url to pdf, also setting a few properties.

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
            doc.Save("Sample.pdf");

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
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Converting an HTML string

Using Select.Pdf Html to Pdf Converter for .NET any html code can be converted to pdf. Select.Pdf Library supports the latest HTML5 with CSS3 style sheets.

To convert a raw html string to pdf, `HtmlToPdf` class exposes the `HtmlToPdf.ConvertHtmlString(string)` and `HtmlToPdf.ConvertHtmlString(string, string)` methods.

The first method, that has only the *url* parameter, can be used to convert html strings that don't have external references to javascript, css files or images. In case the html string contains references to external resources specified using relative paths, the second method needs to be used. `HtmlToPdf.ConvertHtmlString(string, string)` contains an additional parameter: *baseUrl*. The *baseUrl* parameter allows the converter to resolve relative urls. Basically, *baseUrl + relative image/css url = full absolute url*.

### Sample Code

This sample code shows how to use Select.Pdf html to pdf converter to convert an html string to pdf, also setting a few properties.

```csharp
			// instantiate a html to pdf converter object
            HtmlToPdf converter = new HtmlToPdf();

            // set converter options
            converter.Options.PdfPageSize = pageSize;
            converter.Options.PdfPageOrientation = pdfOrientation;
            converter.Options.WebPageWidth = webPageWidth;
            converter.Options.WebPageHeight = webPageHeight;

            // create a new pdf document converting an html string
            PdfDocument doc = converter.ConvertHtmlString(htmlString, baseUrl);

            // save pdf document
            doc.Save("Sample.pdf");

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

    ' create a new pdf document converting an html string
    Dim doc As PdfDocument = converter.ConvertHtmlString(htmlString, baseUrl)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Asynchronous conversion

SelectPdf Html To Pdf Converter for .NET - Community Edition can convert asynchronously. While a web page loads and renders, no thread is held waiting for it, so a web application can have many conversions in flight without a thread for each, and several pages can be rendered at the same time from one method with `await Task.WhenAll(...)`.

An asynchronous conversion produces the same document as the synchronous one. The asynchronous methods are:

- `HtmlToPdf.ConvertUrlAsync(string)` - converts a web page, with an overload that takes a `CancellationToken`.
- `HtmlToPdf.ConvertHtmlStringAsync(string, string)` - converts a html string, with or without a base url, and with overloads that take a `CancellationToken`.

### Converting several pages at the same time

One `HtmlToPdf` object runs one conversion at a time: use a converter for each conversion that runs at the same time. Conversions started together render at the same time, up to `HtmlToPdfOptions.MaximumConcurrentConversions`; the ones past that limit wait for their turn in the order they were started, in the same queue as the synchronous conversions.

```csharp
string[] urls =
{
    "https://selectpdf.com",
    "https://selectpdf.com/pricing/",
    "https://selectpdf.com/html-to-pdf-api/"
};

PdfDocument[] docs = await Task.WhenAll(urls.Select(url =>
{
    HtmlToPdf converter = new HtmlToPdf();
    converter.Options.PdfPageSize = PdfPageSize.A4;
    return converter.ConvertUrlAsync(url);
}));

for (int i = 0; i < docs.Length; i++)
{
    docs[i].Save("page" + i + ".pdf");
    docs[i].Close();
}
```

```vbnet
Dim urls As String() = {
    "https://selectpdf.com",
    "https://selectpdf.com/pricing/",
    "https://selectpdf.com/html-to-pdf-api/"
}

Dim docs As PdfDocument() = Await Task.WhenAll(urls.Select(
    Function(url)
        Dim converter As New HtmlToPdf()
        converter.Options.PdfPageSize = PdfPageSize.A4
        Return converter.ConvertUrlAsync(url)
    End Function))

For i As Integer = 0 To docs.Length - 1
    docs(i).Save("page" & i & ".pdf")
    docs(i).Close()
Next
```

A conversion with html in its header or footer (`PdfHtmlSection`) renders the header and the footer beside the page itself, synchronously or not, so they add little to the conversion time.

### Cancelling a conversion

Cancelling the token stops the conversion wherever it is: a conversion still waiting for its turn leaves the queue, and one that is rendering is stopped in the engine. The task then ends cancelled and no document is produced. A token with a timeout is a simple way to bound how long a conversion may take.

```csharp
using (CancellationTokenSource timeout =
    new CancellationTokenSource(TimeSpan.FromSeconds(30)))
{
    HtmlToPdf converter = new HtmlToPdf();
    try
    {
        PdfDocument doc =
            await converter.ConvertUrlAsync("https://selectpdf.com", timeout.Token);
        doc.Save("document.pdf");
        doc.Close();
    }
    catch (OperationCanceledException)
    {
        // the conversion took longer than 30 seconds
    }
}
```

```vbnet
Using timeout As New CancellationTokenSource(TimeSpan.FromSeconds(30))
    Dim converter As New HtmlToPdf()
    Try
        Dim doc As PdfDocument =
            Await converter.ConvertUrlAsync("https://selectpdf.com", timeout.Token)
        doc.Save("document.pdf")
        doc.Close()
    Catch ex As OperationCanceledException
        ' the conversion took longer than 30 seconds
    End Try
End Using
```

## Converting the current page

A web application often needs to convert the page the user is looking at to pdf with a click of a button, taking into account the values the user has just entered into that page (a filled in form, for example).

Converting the page url with `HtmlToPdf.ConvertUrl(string)` does not do this: the converter fetches the url as a new, anonymous request, so it gets a fresh copy of the page without the user's input (and without their session). The technique is to let the application **render its own page to html** - with the current values in it - and then convert that html string with `HtmlToPdf.ConvertHtmlString(string, string)`.

> **Important:** The second parameter of `HtmlToPdf.ConvertHtmlString(string, string)` is the base url the engine resolves the captured page's *relative* resources against - stylesheets, images, fonts. It must be the application's http origin including the path base and a trailing slash, not a file system path. A file system path loads no stylesheets and produces an unstyled pdf.

### ASP.NET Core (Razor Pages)

The page model renders its own page to a string and hands that string to the converter. Rendering a Razor page to a string is done with the Razor view engine; the complete, reusable service that does it (*ViewRenderService.cs*) ships with the SelectPdf samples, alongside the *CurrentWebPageToPdf* page that uses it.

```csharp
public class CurrentWebPageToPdfModel : PageModel
{
    private readonly IViewRenderService _viewRenderService;

    public CurrentWebPageToPdfModel(IViewRenderService viewRenderService)
    {
        _viewRenderService = viewRenderService;
    }

    // the values the user typed into the page - they are posted back,
    // so they are still set when the page is rendered again below
    [BindProperty]
    public string TxtSampleText { get; set; }

    [BindProperty]
    public bool ChkSampleCheckbox { get; set; }

    public void OnGet()
    {
        TxtSampleText = "sample text";
        ChkSampleCheckbox = true;
    }

    public async Task<IActionResult> OnPost()
    {
        // render THIS page to a html string, with the posted values in it
        string htmlString = await _viewRenderService.RenderToStringAsync(
            "CurrentWebPageToPdf",
            "~/Pages/_Layout.cshtml",
            new CurrentWebPageToPdfModel(_viewRenderService)
            {
                TxtSampleText = this.TxtSampleText,
                ChkSampleCheckbox = this.ChkSampleCheckbox,
            });

        // the http origin the engine resolves the page's relative
        // stylesheets, images and fonts against
        string baseUrl = $"{Request.Scheme}://{Request.Host}{Request.PathBase}/";

        // instantiate a html to pdf converter object
        HtmlToPdf converter = new HtmlToPdf();

        // create a new pdf document converting the html string of the page
        PdfDocument doc = converter.ConvertHtmlString(htmlString, baseUrl);

        // save pdf document
        byte[] pdf = doc.Save();

        // close pdf document
        doc.Close();

        // return resulted pdf document
        FileResult fileResult = new FileContentResult(pdf, "application/pdf");
        fileResult.FileDownloadName = "Document.pdf";
        return fileResult;
    }
}
```

### Converting the captured html

The conversion step itself is the same whatever produced the html string and whatever framework the application uses - so an application that already has the rendered html of a page (from a view engine, a template engine, or its own string building) only needs the following:

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// create a new pdf document converting the html string of the page
// (baseUrl resolves the relative resources referenced by that html)
PdfDocument doc = converter.ConvertHtmlString(htmlString, baseUrl);

// save pdf document
doc.Save("Sample.pdf");

// close pdf document
doc.Close();
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' create a new pdf document converting the html string of the page
' (baseUrl resolves the relative resources referenced by that html)
Dim doc As PdfDocument = converter.ConvertHtmlString(htmlString, baseUrl)

' save pdf document
doc.Save("Sample.pdf")

' close pdf document
doc.Close()
```

## Converting and emailing

It is very simple to convert a web page to PDF using SelectPdf Html to Pdf Converter. We have detailed in two previous sections how to "Converting a URL" (html-to-pdf.md) and how to "Converting an HTML string" (html-to-pdf.md).

After a conversion, the generated PDF document can be saved on disk, can be sent to the browser or can even be sent by email.

### Sample Code

This sample shows the simplest code that can be used to convert an url to pdf using SelectPdf Pdf Library for .NET and then email the generated PDF document as an attachment.

```csharp
	// the message the application shows the user when it is done
	string statusMessage;

	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    try
    {
        // create a new pdf document converting an url
        PdfDocument doc = converter.ConvertUrl(url);

        // create memory stream to save PDF
        MemoryStream pdfStream = new MemoryStream();

        // save pdf document into a MemoryStream
        doc.Save(pdfStream);

        // reset stream position
        pdfStream.Position = 0;

        // create email message
        MailMessage message = new MailMessage();
        message.From = new MailAddress("support@selectpdf.com");
        message.To.Add(new MailAddress(emailAddress));
        message.Subject = "SelectPdf Sample - Convert and Email as Attachment";
        message.Body = "This email should have attached the PDF document " + 
            "resulted from the conversion of the following url to pdf: " + 
            url;
        message.Attachments.Add(new Attachment(pdfStream, "Document.pdf"));

        // send email
        new SmtpClient().Send(message);

        // close pdf document
        doc.Close();

        statusMessage = "Email sent";
    }
    catch (Exception ex)
    {
        statusMessage = "An error occurred: " + ex.Message;
    }
```

```vbnet
        	' the message the application shows the user when it is done
        	Dim statusMessage As String

        	' instantiate a html to pdf converter object
	        Dim converter As New HtmlToPdf()
	
	        Try
	            ' create a new pdf document converting an url
	            Dim doc As PdfDocument = converter.ConvertUrl(url)
	
	            ' create memory stream to save PDF
	            Dim pdfStream As New MemoryStream()
	
	            ' save pdf document into a MemoryStream
	            doc.Save(pdfStream)
	
	            ' reset stream position
	            pdfStream.Position = 0
	
	            ' create email message
	            Dim message As New MailMessage()
	            message.From = New MailAddress("support@selectpdf.com")
	            message.[To].Add(New MailAddress(emailAddress))
	            message.Subject = "SelectPdf Sample - Convert and Email as Attachment"
	            message.Body = "This email should have attached the PDF document " + _
	                "resulted from the conversion of the following url to pdf: " + url
	            message.Attachments.Add(New Attachment(pdfStream, "Document.pdf"))
	
	            ' send email
	            Dim smtp As New SmtpClient()
	            smtp.Send(message)
	
	            ' close pdf document
	            doc.Close()
	
	            statusMessage = "Email sent"
	        Catch ex As Exception
	            statusMessage = "An error occurred: " + ex.Message
	        End Try
```

### Web.config Settings

Remember that System.Net.Mail only works if the SMTP settings are configured. Here is a sample configuration:

```xml
		  <system.net>
		    <mailSettings>
		      <smtp from="test@foo.com">
		        <network host="smtpserver1" port="25" userName="username"
		                 password="secret" defaultCredentials="true" />
		      </smtp>
		    </mailSettings>
		  </system.net>        
```

## Conversion delay and timeouts

For pages that use javascripts heavily, the conversion can be delayed a number of seconds using `HtmlToPdfOptions.ConversionDelay` property of the `HtmlToPdfOptions` object, to allow the content to be fully rendered.

In a similar way, if a page takes too much time to load, the converter can specify the amount of time in seconds when the page load will timeout using the `HtmlToPdfOptions.NavigationTimeout` property of the `HtmlToPdfOptions` object.

`HtmlToPdfOptions.ConversionDelay` and `HtmlToPdfOptions.NavigationTimeout` replace the former `MinPageLoadTime` and `MaxPageLoadTime` properties. The old names are still accepted, as obsolete aliases of the new ones, so existing code keeps compiling.

By default, a navigation timeout raises an exception and no document is produced. Set `HtmlToPdfOptions.RenderPageOnTimeout` to `true` to convert whatever the page has rendered by the time the timeout expires instead. This is useful for pages that keep a connection open or poll indefinitely, where the content is complete long before the browser considers the navigation finished.

### Sample Code

This sample code shows how the html to pdf converter can be used to convert a web page to pdf using Select.Pdf Pdf Library for .NET and how to set the conversion delay and timeout properties.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // specify the number of seconds the conversion is delayed
    converter.Options.ConversionDelay = 2;

    // set the page timeout (in seconds)
    converter.Options.NavigationTimeout = 30;

    // render the page instead of raising an exception if the timeout expires
    converter.Options.RenderPageOnTimeout = true;

    // create a new pdf document converting an url
    PdfDocument doc = converter.ConvertUrl(url);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
	' instantiate a html to pdf converter object
    Dim converter As New HtmlToPdf()

    ' specify the number of seconds the conversion is delayed
    converter.Options.ConversionDelay = 2

    ' set the page timeout (in seconds)
    converter.Options.NavigationTimeout = 30

    ' render the page instead of raising an exception if the timeout expires
    converter.Options.RenderPageOnTimeout = True

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Page settings

Select.Pdf Html to Pdf Converter can be used to control how the pdf pages look like when the pdf document is generated from the conversion of a web page. The most important properties of a pdf page are:

- Page size
- Page orientation
- Page margins

**Page size**

SelectPdf supports the following standard page sizes:

- A0 to A10
- B0 to B5
- ArchA to ArchE
- Letter, HalfLetter, Letter11x17, Note, Flsa, Ledger

To set the page size for the html to pdf conversion, the `HtmlToPdfOptions.PdfPageSize` property of the `HtmlToPdfOptions` object needs to be used. Pages with custom page sizes can also be created using Select.Pdf. A custom page size can be set using the `HtmlToPdfOptions.PdfPageCustomSize` property.

**Page orientation**

SelectPdf supports the following page orientations:

- Portrait
- Landscape

To set the page orientation for the html to pdf conversion, the `HtmlToPdfOptions.PdfPageOrientation` property of the `HtmlToPdfOptions` object needs to be used.

**Page margins**

Page margins can be set using `HtmlToPdfOptions.MarginLeft`, `HtmlToPdfOptions.MarginRight`, `HtmlToPdfOptions.MarginTop`, `HtmlToPdfOptions.MarginBottom` properties of the `HtmlToPdfOptions` object.

### Sample Code

This sample code shows how to use Select.Pdf html to pdf converter to convert an url to pdf, also setting page size, orientation and margins.

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
            doc.Save("Sample.pdf");

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
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

## Resizing content during conversion

Html pages that are converted to pdf and pdf pages in the generated document usually do not have the same size. Because of that, Select.Pdf Library needs to perform some operations on the content rendered from the web page to be able to display it in the pdf page.

**Pdf Page Size**

The size of the pdf pages can be specified using `HtmlToPdfOptions.PdfPageSize` property of the `HtmlToPdfOptions` object. More details about this in the following section: "Page settings" (html-to-pdf.md).

**Web Page Size**

Select.Pdf Html to Pdf Converter has an internal browser that renders the web page just like a regular browser. There are some properties that control how the web page will look in the internal browser:

- `HtmlToPdfOptions.WebPageWidth` - Gets or sets the width of the converted web page as it would appear in the internal browser used to render the html.
  The web page width is specified in pixels and the default value is 1024px. The page is laid out the way a browser window this wide lays it out. How that width maps onto the pdf page is described in Content Resizing below.
  This property can also be set directly in the constructor of `HtmlToPdf` class.
- `HtmlToPdfOptions.WebPageHeight` - Gets or sets the height of the converted web page as it would appear in the internal browser used to render the html.
  The web page height is specified in pixels and the default value is 0px. The height does not limit what is converted: the whole web page is converted and flows onto as many pdf pages as it needs. To leave out the content below this height, set `HtmlToPdfOptions.WebPageFixedSize` to true as well.
  It is the height of the browser window the page is loaded in: the height scripts on the page see (`window.innerHeight`) and what decides which parts of the page count as visible while it loads. With the default value the window is as tall as the area of a pdf page the content is printed in, so scripts see the page they are printed on. With `HtmlToPdfOptions.ManagedContentTrimming` set to false the window has almost no height while the page loads, and content that a script sizes from the window height, or loads or reveals only once it is scrolled into view, can be missing from the generated pdf; for such pages set a height, for example 768.
  The printed layout does not depend on it: CSS viewport units such as `100vh` are resolved against the pdf page.
  This property can also be set directly in the constructor of `HtmlToPdf` class.
- `HtmlToPdfOptions.WebPageFixedSize` - Controls whether the content below `HtmlToPdfOptions.WebPageHeight` is left out of the generated pdf document.
  The default value of this property is false. If it is set to true, the web page is converted only down to `HtmlToPdfOptions.WebPageHeight` pixels, and the content below that height is not included. The width is not affected: content wider than `HtmlToPdfOptions.WebPageWidth` is scaled to fit the pdf page either way.
  A fixed size taller than the space available on the pdf page is spread over as many pages as it needs - it is not squeezed onto a single page. Every page but the last is a full page, and the last one is cut at the height asked for: the content below it is removed from the document, not hidden. A line of text is never cut: when one crosses that height the last page ends above it, so it can be a line shorter than the height asked for. Bookmarks, mapped web elements, links and internal destinations follow the same height.
  Note: `HtmlToPdfOptions.WebPageFixedSize` needs a page height set with `HtmlToPdfOptions.WebPageHeight` - there is no size to fix the browser at otherwise. With the default value (0) this property is ignored. A `HtmlToPdfOptions.PageRanges` set alongside it is honoured within the fixed size.

**Content Resizing**

Because a web page generally has a different width compared with a standard pdf page, the content is scaled to fit the pdf page. As an example, most web sites are optimized for browsers with page widths of at least 1024px or 1280px. A standard A4 page has 595 x 842 points. 1 point is 1/72 inch. 1 pixel is 1/96 inch. This means that an A4 page width is 793px. Because the web page width (1024px or more) is larger than this pdf page width, the rendered content is scaled down to fit the pdf page width.

The converter renders the web page in the internal browser at `HtmlToPdfOptions.WebPageWidth` pixels wide, then scales the result so that width maps to the pdf page content width (the page width minus the left and right margins). A smaller `HtmlToPdfOptions.WebPageWidth` therefore produces larger content in the pdf, and a larger value produces smaller content. Content wider than `HtmlToPdfOptions.WebPageWidth` is not cut off: it is scaled down further, so that the widest content fits the pdf page. Content that is taller than one pdf page flows onto additional pages automatically.

### Sample Code

The following sample code shows the simplest code that can be used to convert an url to pdf:

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

The above code is equivalent with the following sample code where the default values are explicitly specified:

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();
    
    // set converter options
    converter.Options.PdfPageSize = PdfPageSize.A4;
    converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait;

    converter.Options.WebPageWidth = 1024;
    converter.Options.WebPageHeight = 0;
    converter.Options.WebPageFixedSize = false;
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
    converter.Options.WebPageFixedSize = False
    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)
```

## CSS media types

Many web pages define different sets of css styles for browser viewing (*@media screen*) and for printing (*@media print*). Select.Pdf Html to Pdf Converter for .NET can take advantage of this and generate the pdf using the specified media type.

Media type can be set using the `HtmlToPdfOptions.CssMediaType` property of the `HtmlToPdfOptions` object. The default value is `HtmlToPdfCssMediaType.Screen`. The other possible value is `HtmlToPdfCssMediaType.Print`.

### Sample Code

This sample code shows how to convert an url to pdf using Select.Pdf Pdf Library for .NET and also use a media type during the conversion.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set css @media print
    converter.Options.CssMediaType = HtmlToPdfCssMediaType.Print;

    // create a new pdf document converting an url
    PdfDocument doc = converter.ConvertUrl(url);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
	' instantiate a html to pdf converter object
    Dim converter As New HtmlToPdf()

    ' set css @media print
    converter.Options.CssMediaType = HtmlToPdfCssMediaType.Print

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Page breaks

By default, the html to pdf converter offered by Select.Pdf Library automatically pages the content. The rendering engine decides where the page breaks fall, and the pagination is customized from the page's own CSS rather than from converter properties - see the sections below.

### Manual Paging

Content pagination can be controlled from HTML/CSS. CSS styles can be used to manually change the pagination:

**page-break-before: always**

The "page-break-before" property sets whether a page break should occur BEFORE a specified element. The element can be anything (image, table, table row, div, text, etc).

```html
			<div style="page-break-before: always">
			A page break will be inserted BEFORE this div element
			because "page-break-before" property is set to "always".
			</div>        	
```

**page-break-after: always**

The "page-break-after" property sets whether a page break should occur AFTER a specified element. The element can be anything (image, table, table row, div, text, etc).

```html
			<div style="page-break-after: always">
			A page break will be inserted AFTER this div element
			because "page-break-after" property is set to "always".
			</div>        	
```

**page-break-inside: avoid**

The "page-break-inside" property sets whether a page break is allowed INSIDE a specified element. The element can be anything (image, table, table row, div, text, etc).

```html
			<div style="page-break-inside: avoid">
			The content of this div element will not be split into several pages (if possible)
			because "page-break-inside" property is set to "avoid".
			</div>        	
```

## Repeating table headers

Some web pages might contain large html tables that will split over several pages. Select.Pdf Library can be set to repeat the header of the html table at the top of every pdf page that contains a part of that table.

This can be done setting a CSS property for the *thead* element of the html table. The property that needs to be set is: *display: table-header-group*.

### Sample Code

The following html table will repeat the table header at the top of each pdf page when converted to pdf.

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
   <tr>
      <td>February 2009</td>
      <td>$80</td>
   </tr>

   <!--// ..... many table rows that will split over several pages ...... -->

   <tr>
      <td>February 2015</td>
      <td>$200</td>
   </tr>
  </tbody>
</table>
```

## Links

Select.Pdf Library can convert internal html links to internal pdf links and keep external html links (hyperlinks to other pages) the same in pdf (external links). These links can be disabled in pdf if needed.

There are 2 properties that can be used to enable and disable internal and external links:

- `HtmlToPdfOptions.InternalLinksEnabled` - Controls the conversion of internal html links to internal pdf links. The default value for this property is *True*.
- `HtmlToPdfOptions.ExternalLinksEnabled` - Controls the rendering of external hyperlinks in pdf. The default value for this property is *True*.

### Sample Code

This sample code shows how the html to pdf converter can handle internal and external links from the web page when converted to pdf using Select.Pdf Pdf Library for .NET.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set links options
    converter.Options.InternalLinksEnabled = true;
    converter.Options.ExternalLinksEnabled = true;

    // create a new pdf document converting an url
    PdfDocument doc = converter.ConvertUrl(url);

    // save pdf document
    doc.Save("Sample.pdf");

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
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Automatic bookmarks

Select.Pdf Html To Pdf Converter for .NET offers support for pdf bookmarks. It can automatically generate pdf bookmarks based on some html elements selection.

The elements that will be bookmarked are defined using the `HtmlToPdfOptions.PdfBookmarkOptions` property of the `HtmlToPdfOptions` object. This is an instance of the `PdfBookmarkOptions` class, with two properties: `PdfBookmarkOptions.CssSelectors`, which selects the elements, and `PdfBookmarkOptions.HierarchicalBookmarks`, which decides how the bookmarks are arranged.

In other words, the elements that will be bookmarked are defined using CSS selectors. For example, the selector for all the H1 elements is "H1", the selector for all the elements with the CSS class name 'myclass' is "*.myclass" and the selector for the elements with the id 'myid' is "*#myid". Read more about CSS selectors [here](http://www.w3schools.com/cssref/css_selectors.asp).

By default every bookmark is a top-level entry of the outline. Set `PdfBookmarkOptions.HierarchicalBookmarks` to true to nest them in the order of the selectors instead: elements matched by the first selector become top-level entries, elements matched by the second selector are nested under the entry that precedes them, and so on. With the selectors "H1", "H2" each H2 bookmark appears under the H1 before it. A heading that appears before any heading of the level above it is still placed in a valid tree, one level below the deepest entry open at that point.

### Sample Code

This sample code shows how the html to pdf converter can automatically generate pdf bookmarks based on H1 and H2 elements from the web page using Select.Pdf Pdf Library for .NET.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set the css selectors for the automatic bookmarks
    converter.Options.PdfBookmarkOptions.CssSelectors = new string[] {"H1", "H2"};

    // nest the H2 bookmarks under the H1 bookmark that precedes them
    converter.Options.PdfBookmarkOptions.HierarchicalBookmarks = true;

    // display the bookmarks when the document is opened in a viewer
    converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines;

    // create a new pdf document converting an url
    PdfDocument doc = converter.ConvertUrl(url);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
	' instantiate a html to pdf converter object
    Dim converter As New HtmlToPdf()

    ' set the css selectors for the automatic bookmarks
    converter.Options.PdfBookmarkOptions.CssSelectors = New String() {"H1", "H2"}

    ' nest the H2 bookmarks under the H1 bookmark that precedes them
    converter.Options.PdfBookmarkOptions.HierarchicalBookmarks = True

    ' display the bookmarks when the document is opened in a viewer
    converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## PDF document properties

Select.Pdf Html To Pdf Converter for .NET can be used to set pdf document properties, such as:

- Title
- Author
- Subject
- Keywords
- Creation Date

This can be done using `PdfDocument.DocumentInformation` property of the `PdfDocument` object.

What's interesting when it comes to the Html to Pdf Converter, is that Select.Pdf Library can take advantage of the web page properties it converts and set the document properties title, subject and keywords with the corresponding web page properties (title, description, keywords).

To access the web page properties, after the conversion, the `HtmlToPdf` object exposes a property called `HtmlToPdf.ConversionResult`. This is an instance of `HtmlToPdfResult` class and contains details about conversion, including details about the original web page in `HtmlToPdfResult.WebPageInformation` property.

### Sample Code

This sample code shows how to convert a web page to pdf using Select.Pdf and set the basic pdf document information (title, subject, keywords) with the values taken from the webpage.

```csharp
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
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
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
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Viewer preferences

A pdf document can control the way it will be displayed when opened in a pdf viewer. When the pdf document is generated as a result of an html to pdf conversion, these properties can be set using `HtmlToPdfOptions.ViewerPreferences` property of the `HtmlToPdfOptions` object.

The following customizations can be done using the `HtmlToPdfViewerPreferences` object:

| Property | Description |
|---|---|
| HideToolbar | A flag specifying whether to hide the pdf viewer application's tool bars when the document is active |
| HideMenuBar | A flag specifying whether to hide the pdf viewer application's menu bar when the document is active. |
| HideWindowUI | A flag specifying whether to hide user interface elements in the document's window (such as scroll bars and navigation controls), leaving only the document's contents displayed. |
| FitWindow | A flag specifying whether to resize the document's window to fit the size of the first displayed page. |
| CenterWindow | A flag specifying whether to position the document's window in the center of the screen. |
| DisplayDocTitle | A flag specifying whether the window's title bar should display the document title. |
| PageMode | The document page mode when the pdf document is opened in a pdf viewer. Possible values: \| UseNone \| Neither document outline (bookmarks) nor thumbnail images are visible. \| \|---\|---\| \| UseOutlines \| Document outline (bookmarks) are visible. \| \| UseThumbs \| Thumbnail images are visible. \| \| FullScreen \| Full-screen mode, with no menu bar, window controls or any other window visible. \| \| UseOC \| Optional content group panel is visible. \| \| UseAttachments \| Document attachments are visible. \| |
| PageLayout | The page layout to be used when the document is opened. Possible values: \| SinglePage \| Displays one page at a time. \| \|---\|---\| \| OneColumn \| Displays the pages in one column. \| \| TwoColumnLeft \| Displays the pages in two columns, with odd-numbered pages on the left. \| \| TwoColumnRight \| Displays the pages in two columns, with odd-numbered pages on the right. \| |
| NonFullScreenPageMode | The document page mode when the pdf viewer application exits the full screen mode. Possible values: \| UseNone \| Neither document outline (bookmarks) nor thumbnail images are visible. \| \|---\|---\| \| UseOutlines \| Document outline (bookmarks) are visible. \| \| UseThumbs \| Thumbnail images are visible. \| \| UseOC \| Optional content group panel is visible. \| |

### Code Sample

This sample code shows how to convert from html to pdf using Select.Pdf and set the pdf viewer preferences.

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
    converter.Options.ViewerPreferences.NonFullScreenPageMode =
        PdfViewerFullScreenExitMode.UseNone;

    // create a new pdf document converting an url
    PdfDocument doc = converter.ConvertUrl(url);

    // save pdf document
    doc.Save("Sample.pdf");

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
    converter.Options.ViewerPreferences.NonFullScreenPageMode =
        PdfViewerFullScreenExitMode.UseNone

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Security settings

Select.Pdf can be used to set the basic security settings for the generated pdf files and also manage different permissions:

- Owner Password
- User Password
- Assemble Document permission
- Copy Content permission
- Edit Annotations permission
- Edit Content permission
- Fill Form Fields permission
- Print permission

When the pdf document is generated as a result of an html to pdf conversion, these properties can be set using `HtmlToPdfOptions.SecurityOptions` property of the `HtmlToPdfOptions` object.

Setting a password is enough to encrypt the document with AES-256: that is the default of `PdfSecurityOptions.KeySize` and `PdfSecurityOptions.Algorithm`. `PdfSecurityOptions.EncryptMetadata` set to false leaves the XMP metadata packet readable. See "Encrypting regulated data" (html-to-pdf.md).

### Code Sample

This sample code shows how to convert from html to pdf using Select.Pdf, how to set a password to be able to view or modify the document and also specify user permissions for the pdf document (if the user can print, copy content, fill forms, modify, etc).

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set document passwords
    converter.Options.SecurityOptions.OwnerPassword = "test1";
    converter.Options.SecurityOptions.UserPassword = "test2";

    //set document permissions
    converter.Options.SecurityOptions.CanAssembleDocument = false;
    converter.Options.SecurityOptions.CanCopyContent = true;
    converter.Options.SecurityOptions.CanEditAnnotations = true;
    converter.Options.SecurityOptions.CanEditContent = true;
    converter.Options.SecurityOptions.CanFillFormFields = true;
    converter.Options.SecurityOptions.CanPrint = true;
    
    // create a new pdf document converting an url
    PdfDocument doc = converter.ConvertUrl(url);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
	' instantiate a html to pdf converter object
    Dim converter As New HtmlToPdf()

    ' set document passwords
    converter.Options.SecurityOptions.OwnerPassword = "test1"
    converter.Options.SecurityOptions.UserPassword = "test2"

    'set document permissions
    converter.Options.SecurityOptions.CanAssembleDocument = False
    converter.Options.SecurityOptions.CanCopyContent = True
    converter.Options.SecurityOptions.CanEditAnnotations = True
    converter.Options.SecurityOptions.CanEditContent = True
    converter.Options.SecurityOptions.CanFillFormFields = True
    converter.Options.SecurityOptions.CanPrint = True
    
    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Encrypting regulated data

Regulations that cover personal, medical or financial data - HIPAA, GDPR, PCI DSS - do not certify file formats or libraries. Where they ask for encryption they point to current guidance, which names AES (NIST SP 800-111 for stored data). This topic says how to produce pdf documents that meet that bar, and what pdf encryption does and does not protect.

### AES-256 is the default

Setting a password is enough: the document is encrypted with AES and a 256-bit key (revision 6 of the pdf security handler, `PdfEncryptionKeySize.EncryptKey256BitRevision6` with `PdfEncryptionAlgorithm.AES`). This applies to a new document and to the html to pdf converter's `HtmlToPdfOptions.SecurityOptions`.

Every current pdf viewer opens these documents. Viewers older than Adobe Acrobat X (2010) do not; if you must support them, choose AES with a 128-bit key rather than RC4.

### What to avoid

- RC4, with a 40-bit or a 128-bit key. It is broken and is supported only for old viewers.
- An owner password without a user password. Anyone can then open the document; the permissions (printing, copying, editing) are honoured by compliant viewers, but they are not what keeps the content confidential. The user password is.
- Passwords written into source code or configuration files in plain text.

AES-256-GCM (`PdfEncryptionAlgorithm.AESGCM`) is available as well. It is defined by PDF 2.0, so choosing it writes the document as PDF 2.0, which only recent viewers open.

### Metadata

By default everything is encrypted. Set `PdfDocumentSecurity.EncryptMetadata` (or `PdfSecurityOptions.EncryptMetadata` for a conversion) to false to leave the document's XMP metadata packet readable - by a search index or a document management system - while the content stays encrypted. Leave it true when the metadata itself identifies a person: a title such as a patient's name is personal data too. The document information entries (title, author, keywords) are encrypted either way.

### Archiving

PDF/A does not allow encryption, and a PDF/A document with a password is refused. Protect an archived document at the storage level instead.

### Code Sample

```csharp
// html to pdf: a password is enough for AES-256
HtmlToPdf converter = new HtmlToPdf();
converter.Options.SecurityOptions.UserPassword = userPassword;
converter.Options.SecurityOptions.OwnerPassword = ownerPassword;
converter.Options.SecurityOptions.CanCopyContent = false;
PdfDocument doc = converter.ConvertUrl(url);
doc.Save(file);
doc.Close();
```

```vbnet
' html to pdf: a password is enough for AES-256
Dim converter As New HtmlToPdf()
converter.Options.SecurityOptions.UserPassword = userPassword
converter.Options.SecurityOptions.OwnerPassword = ownerPassword
converter.Options.SecurityOptions.CanCopyContent = False
Dim doc As PdfDocument = converter.ConvertUrl(url)
doc.Save(file)
doc.Close()
```

## Other options

Select.Pdf Html to Pdf Converter for .NET offers a lot of customization options. Most of them were described in our developer's guide.

Here is a full list with all **html to pdf converter** options:

`HtmlToPdf.Header` - controls the properties of the custom header of the generated pdf document. This was covered in more details in "Headers and footers" (page-setup-headers-footers.md) section.

`HtmlToPdf.Footer` - controls the properties of the custom footer of the generated pdf document. This was covered in more details in "Headers and footers" (page-setup-headers-footers.md) section.

`HtmlToPdfOptions.AllowContentHeightResize` - Controls whether the page the content ends on is shortened to where the content ends. The default value is True. Set it to False to keep every page of the rendered document at its full height.

`HtmlToPdfOptions.Authentication` - Handles authentication options if the web page being converted requires authentication. This was covered in more details in "Web page authentication" (auth-cookies-headers-post.md) section.

`HtmlToPdfOptions.ChromiumEnginePath` - The full path of the folder containing the Chromium engine binaries. Set it only when the engine is not deployed next to the application. This was covered in more details in the "The Chromium engine" (installation-and-deployment.md) section.

`HtmlToPdfOptions.ChromiumEngineTemporaryFilesPath` - A folder where the Chromium engine writes its temporary files. When not set, the system temporary folder is used. The application needs write permissions on this folder.

`HtmlToPdfOptions.ClickElementsSelectors` - CSS selectors of web page elements that are clicked before the page is converted, for example the close button of a cookie banner that covers the content.

`HtmlToPdfOptions.ClickElementsDelayBefore` - The delay in milliseconds before each element click. The default value is 20 ms.

`HtmlToPdfOptions.ClickElementsDelayAfter` - The delay in milliseconds after each element click. The default value is 200 ms.

`HtmlToPdfOptions.ColorSpace` - Gets or sets the color space of the generated pdf document. This is an instance of `PdfColorSpace` type.

`HtmlToPdfOptions.ConversionDelay` - An additional time in seconds the converter waits after the web page has loaded, before it is rendered. This was covered in more details in "Conversion delay and timeouts" (html-to-pdf.md) section.

`HtmlToPdfOptions.CssMediaType` - Indicates what css styles are used when the web page is rendered. This was covered in more details in "CSS media types" (html-to-pdf.md) section.

`HtmlToPdfOptions.CustomCSS` - CSS styles injected into the converted page, for example to hide an element or to change fonts for print.

`HtmlToPdfOptions.DenyLocalFileAccess` - Prevents the converted page from loading local files. The default value is False. Set it to True when converting HTML that is not trusted; see "Security recommendations" (troubleshooting-and-security.md).

`HtmlToPdfOptions.DisplayFooter` - Controls if a custom footer is displayed in the generated pdf document. This was covered in more details in "Headers and footers" (page-setup-headers-footers.md) section.

`HtmlToPdfOptions.DisplayHeader` - Controls if a custom header is displayed in the generated pdf document. This was covered in more details in "Headers and footers" (page-setup-headers-footers.md) section.

The converter always embeds the fonts needed to render the document, so the text displays correctly on any machine even when those fonts are not installed.

`HtmlToPdfOptions.DrawBackground` - Controls whether the web page background is rendered in the pdf document. The default value is True.

`HtmlToPdfOptions.EnableOfflineMode` - Puts the internal browser in offline mode, so every network request made by the page fails. Useful to convert an HTML string that must not reach the network. The default value is False.

`HtmlToPdfOptions.ExternalLinksEnabled` - Controls the rendering of external hyperlinks in pdf. This was covered in more details in "Links" (html-to-pdf.md) section.

`HtmlToPdfOptions.ForceOutOfProcess` - Runs this conversion in a separate engine process, whatever `GlobalProperties.ChromiumEngineHostMode` selects for the application. Use it to isolate a page that is not trusted, or likely to crash or hang the browser, or to give one conversion settings the in-process host fixes for the whole process at its first conversion (WebGL, the proxy). It costs the start of an engine process. The default value is False.

`HtmlToPdfOptions.HttpCookies` - Gets the collection of custom HTTP cookies used for the conversion. This was covered in more details in "HTTP cookies" (auth-cookies-headers-post.md) section.

`HtmlToPdfOptions.HttpHeaders` - Get the collection of custom HTTP headers used for the conversion. This was covered in more details in "HTTP headers" (auth-cookies-headers-post.md) section.

`HtmlToPdfOptions.HttpPostParameters` - Gets the collection of custom HTTP POST parameters used for the conversion.. This was covered in more details in "HTTP POST requests" (auth-cookies-headers-post.md) section.

`HtmlToPdfOptions.InternalLinksEnabled` - Controls the conversion of internal html links to internal pdf links. This was covered in more details in "Links" (html-to-pdf.md) section.

`HtmlToPdfOptions.JavaScriptEnabled` - Enable scripts when rendering the url or html string.

`HtmlToPdfOptions.JpegCompressionEnabled` - Gets or sets a flag indicating if the JPEG compression is enabled or not for the images in the generated pdf document.

`HtmlToPdfOptions.JpegCompressionLevel` - Gets or sets the compression level of images in the generated pdf document.

`HtmlToPdfOptions.LazyImagesLoadingEnabled` - Scrolls through the page before it is converted, so images and content that load when they are scrolled into view are present in the pdf document. The default value is True.

`HtmlToPdfOptions.LazyImagesLoadingDelay` - The delay in milliseconds after each scroll step when `HtmlToPdfOptions.LazyImagesLoadingEnabled` is True. The default value is 40 ms. Increase it for pages whose lazy content loads slowly.

`HtmlToPdfOptions.LFN` - The path of a diagnostic log file written during the conversion. When not set, no log is written.

`HtmlToPdfOptions.LoginOptions` - Fills in and submits a login form before the page is converted. This was covered in more details in "Web page authentication" (auth-cookies-headers-post.md) section.

`HtmlToPdfOptions.ManagedContentTrimming` - Gets or sets whether the library decides where the rendered content ends on each page. The default value is True. With True, the page the content ends on is drawn only down to where the content ends, on a document of any length. With False, the page is shortened to the content only when the whole content fits on one page. Set it to False if pages are cut in the wrong place.

`HtmlToPdfOptions.ManagedContentTrimmingMode` - Gets or sets how the rendering engine finds where the content ends when `HtmlToPdfOptions.ManagedContentTrimming` is True. The default value is `ManagedContentTrimmingMode.Ink`. With `ManagedContentTrimmingMode.Ink`, the engine measures where the last painted content is after the page is printed, so everything the page paints is kept. With `ManagedContentTrimmingMode.Marker`, the content ends where the page's layout ends, the way earlier versions cut; something painted outside the layout, such as a shadow or a positioned decoration, can be cut.

`HtmlToPdfOptions.MarginBottom` - The bottom margin of the pdf document. The margin is specified in points. 1 point is 1/72 inch.

`HtmlToPdfOptions.MarginLeft` - The left margin of the pdf document. The margin is specified in points. 1 point is 1/72 inch.

`HtmlToPdfOptions.MarginRight` - The right margin of the pdf document. The margin is specified in points. 1 point is 1/72 inch.

`HtmlToPdfOptions.MarginTop` - The top margin of the pdf document. The margin is specified in points. 1 point is 1/72 inch.

`HtmlToPdfOptions.MaxConversionTime` - The time in seconds allowed for rendering the pdf document after the page has loaded. The default value is 60 seconds. `HtmlToPdfOptions.NavigationTimeout` limits the page load before it.

`HtmlToPdfOptions.MaximumConcurrentConversions` - This parameter must be set before the first conversion performed by the application. When this property is set with a negative value or zero, the concurrency level is maximum. The default value is 8. If the maximum number is reached and more conversion requests arrive, they wait in a queue for an available spot and are served in the order they arrived. The limit applies whichever way the engine is hosted (see `GlobalProperties.ChromiumEngineHostMode`).

`HtmlToPdfOptions.NavigationTimeout` - The time in seconds the converter waits for the web page to load. This was covered in more details in "Conversion delay and timeouts" (html-to-pdf.md) section.

`HtmlToPdfOptions.PageRanges` - Restricts the generated pdf document to some of the rendered pages, for example "1", "2-4" or "1-3,5,7-". An empty string (the default) keeps all pages.

`HtmlToPdfOptions.PdfBookmarkOptions` - Gets a reference to the object that controls the bookmarks creation for the generated pdf document. This was covered in more details in "Automatic bookmarks" (html-to-pdf.md) section.

`HtmlToPdfOptions.PdfCompressionLevel` - This property controls the compression level of the generated pdf document.

`HtmlToPdfOptions.PdfDocumentInformation` - Gets a reference to the object that specifies the generated pdf document properties. This was covered in more details in "PDF document properties" (html-to-pdf.md) section.

`HtmlToPdfOptions.PdfPageCustomSize` - This property can be used to specify a custom pdf document page size. This was covered in more details in "Page settings" (html-to-pdf.md) section.

`HtmlToPdfOptions.PdfPageOrientation` - This property controls the page orientation of the generated pdf document pages. This was covered in more details in "Page settings" (html-to-pdf.md) section.

`HtmlToPdfOptions.PdfPageSize` - This property controls the size of the generated document pages. This was covered in more details in "Page settings" (html-to-pdf.md) section.

`HtmlToPdfOptions.PdfStandard` - Defines the pdf standard used by the generated pdf document. This is an instance of `PdfStandard` type. Use it to generate archival documents (PDF/A-1b, PDF/A-2b, PDF/A-3b, PDF/A-3u, PDF/A-4, PDF/A-4e or PDF/A-4f) or PDF/X-1a print documents.

`HtmlToPdfOptions.PostLoadingScript` - JavaScript code injected into the converted page and run after the page has loaded and after its own scripts.

`HtmlToPdfOptions.PostLoadingScriptDelayAfter` - The delay in milliseconds after the post-loading script runs. The default value is 20 ms.

`HtmlToPdfOptions.ProxyOptions` - Gets a reference to an object containing the proxy settings used to access the web page that is being converted. This was covered in more details in "Proxy servers" (auth-cookies-headers-post.md) section.

`HtmlToPdfOptions.RenderPageOnTimeout` - Renders the page as far as it has loaded when `HtmlToPdfOptions.NavigationTimeout` expires, instead of failing the conversion. The default value is False. This was covered in more details in "Conversion delay and timeouts" (html-to-pdf.md) section.

`HtmlToPdfOptions.RenderingEngine` - Selects the HTML rendering engine used by the converter. The only value is `RenderingEngine.Chromium`, which is also the default, so this normally does not need to be set. This was covered in more details in the "The Chromium engine" (installation-and-deployment.md) section.

`HtmlToPdfOptions.SecurityOptions` - Gets a reference to the object that specifies the generated pdf document security settings. This was covered in more details in "Security settings" (html-to-pdf.md) section.

`HtmlToPdfOptions.ViewerPreferences` - Controls how the pdf document will appear in a pdf viewer (like Adobe Reader). This was covered in more details in "Viewer preferences" (html-to-pdf.md) section.

`HtmlToPdfOptions.WebGlEnabled` - Gets or sets a flag indicating if WebGL is enabled in the internal browser. The default value is False; set it to True for pages that draw with WebGL. Turning it off makes conversions noticeably faster, at the cost of not being able to render WebGL content. This was covered in more details in "The Chromium engine" (installation-and-deployment.md) section.

`HtmlToPdfOptions.HttpCache` - Selects what the engine's HTTP cache keeps from this conversion for the next one: `HttpCacheMode.All` (the default) keeps everything, `HttpCacheMode.ThirdParty` keeps only resources from other sites than the converted page's own (fonts, style sheets and scripts served from content delivery networks, for example), and `HttpCacheMode.Clear` starts every conversion with an empty cache. Keeping the cache makes later conversions of pages that share resources faster. It applies when the engine runs inside the application; a conversion in its own engine process always starts with an empty cache. Cookies and site storage are cleared after every conversion whatever the mode.

`HtmlToPdfOptions.WebPageFixedSize` - Controls whether the content below `HtmlToPdfOptions.WebPageHeight` is left out of the generated pdf document. This was covered in more details in "Resizing content during conversion" (html-to-pdf.md) section.

`HtmlToPdfOptions.WebPageHeight` - Gets or sets the height of the converted web page as it would appear in the internal browser used to render the html. This was covered in more details in "Resizing content during conversion" (html-to-pdf.md) section.

`HtmlToPdfOptions.WebPageWidth` - Gets or sets the width of the converted web page as it would appear in the internal browser used to render the html. This was covered in more details in "Resizing content during conversion" (html-to-pdf.md) section.

`HtmlToPdfOptions.WindowStatus` - When set, the converter waits until the page sets `window.status` to this value before converting it. If that does not happen before `HtmlToPdfOptions.NavigationTimeout` expires, the conversion fails with a navigation timeout.
