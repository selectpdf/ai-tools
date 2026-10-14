# HTML to image

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## Converting a URL to an image

Using SelectPdf Html to Image Converter for .NET any web page can be converted to a PNG image. The web page can be specified by an external url or can be a local html file on the disk.

To convert an url (or local file) to image, `HtmlToImage` class exposes the `HtmlToImage.ConvertUrl(string)` method.

### Sample Code

This sample shows how to use SelectPdf html to image converter to convert an url to a PNG image, also setting a few properties.

```csharp
			// read parameters from the webpage
            string url = "https://www.example.com";

            int webPageWidth = 1024;
            try
            {
                webPageWidth = Convert.ToInt32(widthValue);
            }
            catch { }

            int webPageHeight = 0;
            try
            {
                webPageHeight = Convert.ToInt32(heightValue);
            }
            catch { }

            // instantiate a html to image converter object
            HtmlToImage imgConverter = new HtmlToImage();

            // set converter options
            imgConverter.WebPageWidth = webPageWidth;
            imgConverter.WebPageHeight = webPageHeight;

            // convert the url to a PNG image (returned as a byte array)
            byte[] imageBytes = imgConverter.ConvertUrl(url);

            // save the image to a file
            System.IO.File.WriteAllBytes("image.png", imageBytes);
```

```vbnet
        	' read parameters from the webpage
	        Dim url As String = "https://www.example.com"

	        Dim webPageWidth As Integer = 1024
	        Try
	            webPageWidth = Convert.ToInt32(widthValue)
	        Catch
	        End Try

	        Dim webPageHeight As Integer = 0
	        Try
	            webPageHeight = Convert.ToInt32(heightValue)
	        Catch
	        End Try

	        ' instantiate a html to image converter object
	        Dim imgConverter As New HtmlToImage()

	        ' set converter options
	        imgConverter.WebPageWidth = webPageWidth
	        imgConverter.WebPageHeight = webPageHeight

	        ' convert the url to a PNG image (returned as a byte array)
	        Dim imageBytes As Byte() = imgConverter.ConvertUrl(url)

	        ' save the image to a file
	        System.IO.File.WriteAllBytes("image.png", imageBytes)
```

## Converting an HTML string to an image

Using SelectPdf Html to Image Converter for .NET any html code can be converted to a PNG image. SelectPdf Library supports the latest HTML5 with CSS3 style sheets.

To convert a raw html string to pdf, `HtmlToImage` class exposes the `HtmlToImage.ConvertHtmlString(string)` and `HtmlToImage.ConvertHtmlString(string, string)` methods.

The first method, that has only the *htmlString* parameter, can be used to convert html strings that don't have external references to javascript, css files or images. In case the html string contains references to external resources specified using relative paths, the second method needs to be used. `HtmlToImage.ConvertHtmlString(string, string)` contains an additional parameter: *baseUrl*. The *baseUrl* parameter allows the converter to resolve relative urls. Basically, *baseUrl + relative image/css url = full absolute url*.

### Sample Code

This sample shows how to use SelectPdf html to image converter to convert a raw html code to a PNG image, also setting a few properties.

```csharp
			// read parameters from the webpage
            string htmlString = "<html><body><h1>Hello world</h1></body></html>";
            string baseUrl = "https://www.example.com/";

            int webPageWidth = 1024;
            try
            {
                webPageWidth = Convert.ToInt32(widthValue);
            }
            catch { }

            int webPageHeight = 0;
            try
            {
                webPageHeight = Convert.ToInt32(heightValue);
            }
            catch { }

            // instantiate a html to image converter object
            HtmlToImage imgConverter = new HtmlToImage();

            // set converter options
            imgConverter.WebPageWidth = webPageWidth;
            imgConverter.WebPageHeight = webPageHeight;

            // convert the html string to a PNG image (returned as a byte array)
            byte[] imageBytes =
                imgConverter.ConvertHtmlString(htmlString, baseUrl);

            // save the image to a file
            System.IO.File.WriteAllBytes("image.png", imageBytes);
```

```vbnet
        	' read parameters from the webpage
	        Dim htmlString As String = "<html><body><h1>Hello world</h1></body></html>"
	        Dim baseUrl As String = "https://www.example.com/"

	        Dim webPageWidth As Integer = 1024
	        Try
	            webPageWidth = Convert.ToInt32(widthValue)
	        Catch
	        End Try

	        Dim webPageHeight As Integer = 0
	        Try
	            webPageHeight = Convert.ToInt32(heightValue)
	        Catch
	        End Try

	        ' instantiate a html to image converter object
	        Dim imgConverter As New HtmlToImage()

	        ' set converter options
	        imgConverter.WebPageWidth = webPageWidth
	        imgConverter.WebPageHeight = webPageHeight

	        ' convert the html string to a PNG image (returned as a byte array)
	        Dim imageBytes As Byte() = _
	            imgConverter.ConvertHtmlString(htmlString, baseUrl)

	        ' save the image to a file
	        System.IO.File.WriteAllBytes("image.png", imageBytes)
```
