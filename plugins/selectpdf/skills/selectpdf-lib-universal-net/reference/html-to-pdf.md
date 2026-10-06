# HTML to PDF conversion

Converted from the shipped SelectPdf.Universal v26.4 help topics. C# and VB.NET samples are the official ones.

## The HTML to PDF converter

One of the most important features of Select.Pdf Library for .NET is the **Html to Pdf Converter**.

HTML content can be added to a pdf document in several ways. If a complex document is created that will contain several types of pdf elements and the html content that will be added is only a small part of the total content, `PdfHtmlElement` objects can be used to convert web pages or raw html content and add the conversion result to the pdf document. This is better described here.

If the pdf is generated mostly as a result of an html to pdf conversion, the `HtmlToPdf` object should be used instead. The `HtmlToPdf` and related classes and properties will be described in this page and in all pages under "Html to Pdf Converter" of Select.Pdf Developers Guide.

### Quick Start

Using the Html to Pdf Converter is very easy. The first thing that needs to be done is the namespace importing.

```csharp
			using SelectPdf.Universal;
```

```vbnet
			Imports SelectPdf.Universal
```

Sample code that shows how to convert an url to pdf using Select.Pdf Library for .NET:

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

Sample code that shows how to convert raw html code to pdf using Select.Pdf Library for .NET:

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

The first method, that has only the *htmlString* parameter, can be used to convert html strings that don't have external references to javascript, css files or images. In case the html string contains references to external resources specified using relative paths, the second method needs to be used. `HtmlToPdf.ConvertHtmlString(string, string)` contains an additional parameter: *baseUrl*. The *baseUrl* parameter allows the converter to resolve relative urls. Basically, *baseUrl + relative image/css url = full absolute url*.

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

SelectPdf Html To Pdf Converter for .NET can convert asynchronously. While a web page loads and renders, no thread is held waiting for it, so a web application can have many conversions in flight without a thread for each, and several pages can be rendered at the same time from one method with `await Task.WhenAll(...)`.

An asynchronous conversion produces the same document as the synchronous one. The asynchronous methods are:

- `HtmlToPdf.ConvertUrlAsync(string)` - converts a web page, with an overload that takes a `CancellationToken`.
- `HtmlToPdf.ConvertHtmlStringAsync(string, string)` - converts a html string, with or without a base url, and with overloads that take a `CancellationToken`.
- `HtmlToImage.ConvertUrlAsync(string)` and `HtmlToImage.ConvertHtmlStringAsync(string, string)` - the same for html to image.
- `PdfCanvas.AddAsync(PdfPageElement)` - adds an element to a page or a template without waiting for it to render (see below).

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

### Adding html elements to a document asynchronously

A document built from several html elements (`PdfHtmlElement`, `HtmlToImageElement`) can render them all at the same time with `PdfCanvas.AddAsync(PdfPageElement)`. Each element starts rendering as soon as it is added, and the elements are placed in the document one after the other, in the order they were added, as they would be with `PdfCanvas.Add(PdfPageElement)`.

Until an element has been placed, its position in the document is not known. An element positioned from the result of another one - for example right below its `PdfRenderingResult.PdfPageLastRectangle` - must await that element first, or be placed on a page of its own. Saving, closing or appending the document, a synchronous `Add` and the members of `PdfDocument.Pages` wait for the pending additions before they run.

```csharp
PdfDocument doc = new PdfDocument();
PdfPage page1 = doc.AddPage();
PdfPage page2 = doc.AddPage();

// both regions render at the same time
Task<PdfRenderingResult> first =
    page1.AddAsync(new PdfHtmlElement("https://selectpdf.com"));
Task<PdfRenderingResult> second =
    page2.AddAsync(new PdfHtmlElement("https://selectpdf.com/pricing/"));

// a text placed below the first region waits for it
PdfRenderingResult result = await first;
PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
font.Size = 10;
await page1.AddAsync(new PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 10,
    "Captured with SelectPdf", font));

await second;
doc.Save("document.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
Dim page1 As PdfPage = doc.AddPage()
Dim page2 As PdfPage = doc.AddPage()

' both regions render at the same time
Dim first As Task(Of PdfRenderingResult) =
    page1.AddAsync(New PdfHtmlElement("https://selectpdf.com"))
Dim second As Task(Of PdfRenderingResult) =
    page2.AddAsync(New PdfHtmlElement("https://selectpdf.com/pricing/"))

' a text placed below the first region waits for it
Dim result As PdfRenderingResult = Await first
Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
font.Size = 10
Await page1.AddAsync(New PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 10,
    "Captured with SelectPdf", font))

Await second
doc.Save("document.pdf")
doc.Close()
```

Cancelling the token given to `PdfCanvas.AddAsync(PdfPageElement, CancellationToken)` stops an element that has not been placed yet; the elements added after it are placed as usual. Do not change an element after adding it until its task has completed.

## Converting multiple URLs into one document

Select.Pdf Library for .NET can be used to convert multiple urls (or html strings) into the same pdf document. There are 2 approaches to this task:

- Using `HtmlToPdf` main converter object to generate individual documents for each web page and then using the method described here to merge the the individual pdf documents into a final pdf document.
- Using `PdfHtmlElement` described here, adding several objects of this type, one for each conversion, to the same `PdfDocument` object.

The first method has the disadvange that all individual web page conversions will appear in the final pdf starting with a new page, while using the second method, the successive conversions can be added on new pages or at the end of the previous content on the same page of the pdf document.

### Sample Code

This sample code shows how to convert 3 individual urls into the same pdf document using `HtmlToPdf` approach.

```csharp
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
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();

            // close the original pdf documents
            doc1.Close();
            doc2.Close();
            doc3.Close();
```

```vbnet
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
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()

            ' close the original pdf documents
            doc1.Close()
            doc2.Close()
            doc3.Close()
```

This sample code shows how to convert 3 individual urls into the same pdf document using `PdfHtmlElement` approach.

```csharp
	// create a new pdf document 
    PdfDocument doc = new PdfDocument();

    // add a new page to the document
    PdfPage page = doc.AddPage();

    // add the first url
    PdfHtmlElement html1 = new PdfHtmlElement(url1);
    PdfRenderingResult result = page.Add(html1);

    // get the last page of the previous conversion
    PdfPage page2 = doc.Pages[result.PdfPageLastIndex];

    // add the second url
    PdfHtmlElement html2 = new PdfHtmlElement(result.PdfPageLastRectangle.Left, 
        result.PdfPageLastRectangle.Bottom, url2);
    result = page2.Add(html2);

    // get the last page of the previous conversion
    PdfPage page3 = doc.Pages[result.PdfPageLastIndex];

    // add the third url
    PdfHtmlElement html3 = new PdfHtmlElement(result.PdfPageLastRectangle.Left, 
        result.PdfPageLastRectangle.Bottom, url3);
    result = page3.Add(html3);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
			' create a new pdf document 
            Dim doc As New PdfDocument()

            ' add a new page to the document
            Dim page As PdfPage = doc.AddPage()

            ' add the first url
            Dim html1 As New PdfHtmlElement(url1)
            Dim result As PdfRenderingResult = page.Add(html1)

            ' get the last page of the previous conversion
            Dim page2 As PdfPage = doc.Pages(result.PdfPageLastIndex)

            ' add the second url
            Dim html2 As New PdfHtmlElement(result.PdfPageLastRectangle.Left, _
                                            result.PdfPageLastRectangle.Bottom, url2)
            result = page2.Add(html2)

            ' get the last page of the previous conversion
            Dim page3 As PdfPage = doc.Pages(result.PdfPageLastIndex)

            ' add the third url
            Dim html3 As New PdfHtmlElement(result.PdfPageLastRectangle.Left, _
                                            result.PdfPageLastRectangle.Bottom, url3)
            result = page3.Add(html3)

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
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

## Starting the conversion from JavaScript

Select.Pdf exposes a global Javascript variable called *selectpdf* in the page that is being converted. Using this variable you can check if the javascript code is executed inside the converter (check if *typeof(selectpdf) == "object"*). The converter version can also be checked using *selectpdf.version*.

The most important feature of the Javascript interface is the possibility to manually start the page conversion to pdf with a javascript call. To do that, you must:

1. Set the `HtmlToPdfOptions.StartupMode` property of the `HtmlToPdfOptions` object to `HtmlToPdfStartupMode.Manual`.
2. Call *selectpdf.start()* from javascript.

Important:

- If `HtmlToPdfOptions.StartupMode` is set to `HtmlToPdfStartupMode.Manual` and *selectpdf.start()* is not called from javascript, the conversion will timeout.
- If `HtmlToPdfOptions.StartupMode` is set to the default value `HtmlToPdfStartupMode.Automatic`, the conversion will start without waiting for any javascript calls.

### Sample Code

Below it's the html of a test page that waits for 3 seconds after it loads and then calls the javascript conversion method. Converting it in `HtmlToPdfStartupMode.Manual` mode will display the time elapsed. Converting it in `HtmlToPdfStartupMode.Automatic` mode will not show that 3 seconds time interval.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Test document for Select.Pdf Sample</title>
    </head>
    <body>
        <h1>Select.Pdf for .NET - Start Conversion from Javascript</h1>
        <p>
            This is a sample page that will demonstrate how a html to pdf conversion
            can be triggered from a javascript call
            using Select.Pdf Library for .NET.
            <br />
            The page increments a timer until it reaches 3
            and then it calls the conversion from javascript.
            If the conversion is manually started from javascript,
            the timer below should be 3 in pdf.
            If the conversion is automatically started when the page loads,
            the timer below should be 1 in pdf.
        </p>
        <p>
            Library version: <span id="idVersion"></span><br />
            Timer: <span id="idTimer"></span><br />
        </p>

        <script type="text/javascript" src="jquery.js"> </script>
        <script type="text/javascript">
            var timer = 0;

            function incrementTimer() {
                timer = timer + 1;

                if (typeof (selectpdf) == "object") {
                    $("*#idVersion").html(selectpdf.version);
                }
                else {
                    $("*#idVersion").html("Not in converter.");
                }
                $("*#idTimer").text(timer);

                if (timer == 3) {
                    // 3 seconds elapsed - start conversion
                    if (typeof (selectpdf) == "object") {
                        selectpdf.start();
                    }
                }
                else {
                    // wait another second
                    setTimeout("incrementTimer()", 1000);
                }

            }

            $(document).ready(function () {
                incrementTimer();
            });
        </script>
    </body>
</html>
```

This sample code shows how to manually start the html to pdf conversion of the Select.Pdf Library for .NET using a Javascript call. The *url* parameter points to the page above.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set startup mode
    converter.Options.StartupMode = HtmlToPdfStartupMode.Manual;

    // set timeout
    converter.Options.NavigationTimeout = 10;

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

    ' set startup mode
    converter.Options.StartupMode = HtmlToPdfStartupMode.Manual

    ' set timeout
    converter.Options.NavigationTimeout = 10

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
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

## Partial page conversion

Select.Pdf can render only a specific section of a web page - an image, a table, a table row, a div - instead of the whole page. The section is identified by a **CSS selector**, set through the *VisibleWebElementSelector* property. The property is available on every object that drives the rendering engine:

- `HtmlToPdfOptions.VisibleWebElementSelector`
- `PdfHtmlElement.VisibleWebElementSelector`
- `PdfHtmlSection.VisibleWebElementSelector`
- `HtmlToImage.VisibleWebElementSelector`
- `HtmlToImageElement.VisibleWebElementSelector`

Any selector the browser understands can be used - `#invoice` for an element id, `.infobox` for a class, `table.wikitable tbody` for a nested element. The first match is rendered.

> **Note:** A selector that matches nothing, or that matches an element with no size, **fails the conversion** with `HtmlToPdfException`, rather than quietly producing the whole page.

> **Important:** `HtmlToPdfOptions.VisibleWebElementId` and the matching properties on the other objects are **obsolete**. They accept an element id only and are kept so that existing code keeps compiling: setting the id `x` is the same as setting the selector `#x`. Reading the property returns the id when the selector is a plain id, and *null* otherwise - a selector such as `.infobox` has no id to report. Use the selector property in new code.

### Convert only a section of a page to pdf

The engine lifts the selected element out of the page and prints it on its own. The text stays real text - it is selectable and searchable in the produced pdf.

> **Note:** The generated document keeps the page size the converter was asked for (`HtmlToPdfOptions.PdfPageSize` / `HtmlToPdfOptions.PdfPageCustomSize`, A4 by default), so the section is rendered at the top of a normal page rather than on a sheet trimmed to its height. Margins, orientation and the header/footer bands keep working as usual. To get a document exactly as tall as the section, set `HtmlToPdfOptions.PdfPageCustomSize` to the size you need.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// render only the element matched by this css selector
converter.Options.VisibleWebElementSelector = "#invoice";

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

' render only the element matched by this css selector
converter.Options.VisibleWebElementSelector = "#invoice"

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("Sample.pdf")

' close pdf document
doc.Close()
```

### Render a page section to an image

On the image path the resulting picture is exactly as large as the selected element.

```csharp
// instantiate a html to image converter object
HtmlToImage converter = new HtmlToImage();

// specify the section that will be rendered
converter.VisibleWebElementSelector = "table.wanted";

// render the section - the png contains only that element
byte[] image = converter.ConvertUrl(url);

System.IO.File.WriteAllBytes("Section.png", image);
```

```vbnet
' instantiate a html to image converter object
Dim converter As New HtmlToImage()

' specify the section that will be rendered
converter.VisibleWebElementSelector = "table.wanted"

' render the section - the png contains only that element
Dim image As Byte() = converter.ConvertUrl(url)

System.IO.File.WriteAllBytes("Section.png", image)
```

### Place a page section into a pdf document

`PdfHtmlElement` renders the section and draws it at a chosen position on a page of a pdf document, keeping the text real. `HtmlToImageElement` does the same but draws the section as a picture, so its text is not selectable or searchable in the produced pdf.

```csharp
// create a new pdf document
PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

// render only the given section of the page and draw it at (0, 0)
PdfHtmlElement section = new PdfHtmlElement(0, 0, url);
section.VisibleWebElementSelector = "#invoice";
page.Add(section);

// save pdf document
doc.Save("Sample.pdf");

// close pdf document
doc.Close();
```

```vbnet
' create a new pdf document
Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

' render only the given section of the page and draw it at (0, 0)
Dim section As New PdfHtmlElement(0, 0, url)
section.VisibleWebElementSelector = "#invoice"
page.Add(section)

' save pdf document
doc.Save("Sample.pdf")

' close pdf document
doc.Close()
```

### Removing parts of a page instead

To keep the page but drop some of its parts, use `HtmlToPdfOptions.HiddenWebElements`, which hides the elements you name before the page is printed - see "Excluding elements" (html-to-pdf.md).

## Excluding elements

Select.Pdf Html to Pdf Converter for .NET can be used to exclude certain elements from the conversion when a web page is converted to pdf.

The elements that will be excluded are defined using the `HtmlToPdfOptions.HiddenWebElements` property of the `HtmlToPdfOptions` object. This is an instance of the `HiddenWebElements` class, whose single property is `HiddenWebElements.CssSelectors`.

In other words, the elements that will be excluded are defined using CSS selectors. For example, the selector for all the H1 elements is "H1", the selector for all the elements with the CSS class name 'myclass' is "*.myclass" and the selector for the elements with the id 'myid' is "*#myid". Read more about CSS selectors [here](http://www.w3schools.com/cssref/css_selectors.asp).

### Sample Code

This sample code shows how the html to pdf converter can exclude all images when the web page is converted to pdf.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // exclude all images
    converter.Options.HiddenWebElements.CssSelectors = new string[] {"img"};

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

    ' exclude all images
    converter.Options.HiddenWebElements.CssSelectors = New String() {"img"}

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

## PDF document properties

As described in "Document properties" (pdf-creation-elements.md) page, Select.Pdf Library for .NET can be used to set pdf document properties, such as:

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

Setting a password is enough to encrypt the document with AES-256: that is the default of `PdfSecurityOptions.KeySize` and `PdfSecurityOptions.Algorithm`. `PdfSecurityOptions.EncryptMetadata` set to false leaves the XMP metadata packet readable. See "Encrypting regulated data" (security-and-signatures.md).

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

## Other options

Select.Pdf Html to Pdf Converter for .NET offers a lot of customization options. Most of them were described in our developer's guide.

Here is a full list with all **html to pdf converter** options:

`HtmlToPdf.Header` - controls the properties of the custom header of the generated pdf document. This was covered in more details in "Headers and footers in HTML to PDF" (page-setup-headers-footers.md) section.

`HtmlToPdf.Footer` - controls the properties of the custom footer of the generated pdf document. This was covered in more details in "Headers and footers in HTML to PDF" (page-setup-headers-footers.md) section.

`HtmlToPdfOptions.AccessibilityStandard` - The accessibility standard the generated document declares: PDF/UA-1 or PDF/UA-2 (`PdfUAStandard`). Declaring a standard turns tagging on; PDF/UA-2 also writes the document as PDF 2.0. This was covered in more details in the "Accessible PDF and PDF/A" (accessibility-and-compliance.md) section.

`HtmlToPdfOptions.AllowContentHeightResize` - Controls whether the page the content ends on is shortened to where the content ends. The default value is True. Set it to False to keep every page of the rendered document at its full height.

`HtmlToPdfOptions.Authentication` - Handles authentication options if the web page being converted requires authentication. This was covered in more details in "Web page authentication" (web-elements-and-auth.md) section.

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

`HtmlToPdfOptions.DisplayFooter` - Controls if a custom footer is displayed in the generated pdf document. This was covered in more details in "Headers and footers in HTML to PDF" (page-setup-headers-footers.md) section.

`HtmlToPdfOptions.DisplayHeader` - Controls if a custom header is displayed in the generated pdf document. This was covered in more details in "Headers and footers in HTML to PDF" (page-setup-headers-footers.md) section.

The converter always embeds the fonts needed to render the document, so the text displays correctly on any machine even when those fonts are not installed.

`HtmlToPdfOptions.DrawBackground` - Controls whether the web page background is rendered in the pdf document. The default value is True.

`HtmlToPdfOptions.EnableOfflineMode` - Puts the internal browser in offline mode, so every network request made by the page fails. Useful to convert an HTML string that must not reach the network. The default value is False.

`HtmlToPdfOptions.ExternalLinksEnabled` - Controls the rendering of external hyperlinks in pdf. This was covered in more details in "Links in HTML to PDF" (links-bookmarks-toc.md) section.

`HtmlToPdfOptions.ForceOutOfProcess` - Runs this conversion in a separate engine process, whatever `GlobalProperties.ChromiumEngineHostMode` selects for the application. Use it to isolate a page that is not trusted, or likely to crash or hang the browser, or to give one conversion settings the in-process host fixes for the whole process at its first conversion (WebGL, the proxy). It costs the start of an engine process. The default value is False.

`HtmlToPdfOptions.GenerateSinglePagePdf` - Generates a single-page pdf document: the page is resized to hold the whole content. The default value is False.

`HtmlToPdfOptions.HiddenWebElements` - Gets a reference to the object that controls the visibility of some web elements in the generated pdf document. This was covered in more details in "Excluding elements" (html-to-pdf.md) section.

`HtmlToPdfOptions.HttpCookies` - Gets the collection of custom HTTP cookies used for the conversion. This was covered in more details in "HTTP cookies" (web-elements-and-auth.md) section.

`HtmlToPdfOptions.HttpHeaders` - Get the collection of custom HTTP headers used for the conversion. This was covered in more details in "HTTP headers" (web-elements-and-auth.md) section.

`HtmlToPdfOptions.HttpPostParameters` - Gets the collection of custom HTTP POST parameters used for the conversion.. This was covered in more details in "HTTP POST requests" (web-elements-and-auth.md) section.

`HtmlToPdfOptions.InternalLinksEnabled` - Controls the conversion of internal html links to internal pdf links. This was covered in more details in "Links in HTML to PDF" (links-bookmarks-toc.md) section.

`HtmlToPdfOptions.JavaScriptEnabled` - Enable scripts when rendering the url or html string.

`HtmlToPdfOptions.JpegCompressionEnabled` - Gets or sets a flag indicating if the JPEG compression is enabled or not for the images in the generated pdf document.

`HtmlToPdfOptions.JpegCompressionLevel` - Gets or sets the compression level of images in the generated pdf document.

`HtmlToPdfOptions.LazyImagesLoadingEnabled` - Scrolls through the page before it is converted, so images and content that load when they are scrolled into view are present in the pdf document. The default value is True.

`HtmlToPdfOptions.LazyImagesLoadingDelay` - The delay in milliseconds after each scroll step when `HtmlToPdfOptions.LazyImagesLoadingEnabled` is True. The default value is 40 ms. Increase it for pages whose lazy content loads slowly.

`HtmlToPdfOptions.LFN` - The path of a diagnostic log file written during the conversion. When not set, no log is written.

`HtmlToPdfOptions.LoginOptions` - Fills in and submits a login form before the page is converted. This was covered in more details in "Web page authentication" (web-elements-and-auth.md) section.

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

`HtmlToPdfOptions.PdfBookmarkOptions` - Gets a reference to the object that controls the bookmarks creation for the generated pdf document. This was covered in more details in "Automatic bookmarks" (links-bookmarks-toc.md) section.

`HtmlToPdfOptions.PdfCompressionLevel` - This property controls the compression level of the generated pdf document.

`HtmlToPdfOptions.PdfDocumentInformation` - Gets a reference to the object that specifies the generated pdf document properties. This was covered in more details in "PDF document properties" (html-to-pdf.md) section.

`HtmlToPdfOptions.PdfPageCustomSize` - This property can be used to specify a custom pdf document page size. This was covered in more details in "Page settings" (html-to-pdf.md) section.

`HtmlToPdfOptions.PdfPageOrientation` - This property controls the page orientation of the generated pdf document pages. This was covered in more details in "Page settings" (html-to-pdf.md) section.

`HtmlToPdfOptions.PdfPageSize` - This property controls the size of the generated document pages. This was covered in more details in "Page settings" (html-to-pdf.md) section.

`HtmlToPdfOptions.PdfStandard` - Defines the pdf standard used by the generated pdf document. This is an instance of `PdfStandard` type. Use it to generate archival documents (PDF/A-1b to PDF/A-4, including the accessible PDF/A-3a) or PDF/X-1a print documents. This was covered in more details in the "Accessible PDF and PDF/A" (accessibility-and-compliance.md) section.

`HtmlToPdfOptions.Tagged` - Produces a tagged, accessible (PDF/UA) document with a logical structure tree. This was covered in more details in the "Accessible PDF and PDF/A" (accessibility-and-compliance.md) section.

`HtmlToPdfOptions.Title` - The document title recorded in the metadata of a tagged PDF. When not set, the HTML document title is used.

`HtmlToPdfOptions.Language` - The natural language (e.g. "en-US") of a tagged PDF, recorded as the document language.

`HtmlToPdfOptions.PostLoadingScript` - JavaScript code injected into the converted page and run after the page has loaded and after its own scripts.

`HtmlToPdfOptions.PostLoadingScriptDelayAfter` - The delay in milliseconds after the post-loading script runs. The default value is 20 ms.

`HtmlToPdfOptions.ProxyOptions` - Gets a reference to an object containing the proxy settings used to access the web page that is being converted. This was covered in more details in "Proxy servers" (web-elements-and-auth.md) section.

`HtmlToPdfOptions.RenderPageOnTimeout` - Renders the page as far as it has loaded when `HtmlToPdfOptions.NavigationTimeout` expires, instead of failing the conversion. The default value is False. This was covered in more details in "Conversion delay and timeouts" (html-to-pdf.md) section.

`HtmlToPdfOptions.RenderingEngine` - Selects the HTML rendering engine used by the converter. The only value is `RenderingEngine.Chromium`, which is also the default, so this normally does not need to be set. This was covered in more details in the "The Chromium engine" (installation-and-deployment.md) section.

`HtmlToPdfOptions.SecurityOptions` - Gets a reference to the object that specifies the generated pdf document security settings. This was covered in more details in "Security settings" (html-to-pdf.md) section.

`HtmlToPdfOptions.StartupMode` - Use this property to specify how the conversion starts. This was covered in more details in "Starting the conversion from JavaScript" (html-to-pdf.md) section.

`HtmlToPdfOptions.StartupScript` - Use this property to specify some JavaScript code that will be injected into the page that is converted.

`HtmlToPdfOptions.ViewerPreferences` - Controls how the pdf document will appear in a pdf viewer (like Adobe Reader). This was covered in more details in "Viewer preferences" (html-to-pdf.md) section.

`HtmlToPdfOptions.VisibleWebElementSelector` - Use this property to convert only a certain section of the page, specified by a css selector. `HtmlToPdfOptions.VisibleWebElementId` is the obsolete form of the same option and accepts an element id only. This was covered in more details in "Partial page conversion" (html-to-pdf.md) section.

`HtmlToPdfOptions.WebElementsMappingOptions` - Gets a reference to the object controlling the retrieval of HTML elements position in the generated PDF. In order to retrieve the position in PDF for a list of HTML elements, the CssSelectors property of WebElementsMappingOptions must be set with an array of strings containing the selectors of the HTML elements. The positions of the HTML elements in the generated PDF document will be returned in the Result object. This was covered in more details in "Web element locations" (web-elements-and-auth.md) section.

`HtmlToPdfOptions.WebGlEnabled` - Gets or sets a flag indicating if WebGL is enabled in the internal browser. The default value is False; set it to True for pages that draw with WebGL. Turning it off makes conversions noticeably faster, at the cost of not being able to render WebGL content. This was covered in more details in "The Chromium engine" (installation-and-deployment.md) section.

`HtmlToPdfOptions.HttpCache` - Selects what the engine's HTTP cache keeps from this conversion for the next one: `HttpCacheMode.All` (the default) keeps everything, `HttpCacheMode.ThirdParty` keeps only resources from other sites than the converted page's own (fonts, style sheets and scripts served from content delivery networks, for example), and `HttpCacheMode.Clear` starts every conversion with an empty cache. Keeping the cache makes later conversions of pages that share resources faster. It applies when the engine runs inside the application; a conversion in its own engine process always starts with an empty cache. Cookies and site storage are cleared after every conversion whatever the mode.

`HtmlToPdfOptions.WebPageFixedSize` - Controls whether the content below `HtmlToPdfOptions.WebPageHeight` is left out of the generated pdf document. This was covered in more details in "Resizing content during conversion" (html-to-pdf.md) section.

`HtmlToPdfOptions.WebPageHeight` - Gets or sets the height of the converted web page as it would appear in the internal browser used to render the html. This was covered in more details in "Resizing content during conversion" (html-to-pdf.md) section.

`HtmlToPdfOptions.WebPageWidth` - Gets or sets the width of the converted web page as it would appear in the internal browser used to render the html. This was covered in more details in "Resizing content during conversion" (html-to-pdf.md) section.

`HtmlToPdfOptions.WindowStatus` - When set, the converter waits until the page sets `window.status` to this value before converting it. If that does not happen before `HtmlToPdfOptions.NavigationTimeout` expires, the conversion fails with a navigation timeout.
