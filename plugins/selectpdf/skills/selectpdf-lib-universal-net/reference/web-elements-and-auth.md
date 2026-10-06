# Web element locations, authentication, cookies, headers, POST and proxy

Converted from the shipped SelectPdf.Universal v26.4 help topics. C# and VB.NET samples are the official ones.

## Web element locations

SelectPdf Html to Pdf Converter for .NET can be used to locate certain web elements in the generated PDF document.

The web elements for which the locations are retrieved are defined using the `HtmlToPdfOptions.WebElementsMappingOptions` property of the `HtmlToPdfOptions` object. This is an instance of the `WebElementsMappingOptions` class that has 2 properties: `WebElementsMappingOptions.CssSelectors` that can be used to specify the web elements and `WebElementsMappingOptions.Result`, which is set automatically after conversion and returns the mapping into the generated pdf document of the html elements specified by the `WebElementsMappingOptions.CssSelectors` property.

In other words, the web elements are defined using CSS selectors. For example, the selector for all the H1 elements is "H1", the selector for all the elements with the CSS class name 'myclass' is "*.myclass" and the selector for the elements with the id 'myid' is "*#myid". Read more about CSS selectors [here](http://www.w3schools.com/cssref/css_selectors.asp).

### Sample Code

This sample shows how the html to pdf converter can locate certain web elements in the generated PDF using SelectPdf Pdf Library for .NET. The specified web elements will be marked/highlighted in PDF with a blue rectangle.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set the css selectors for the web elements
    // whose positions will be retrieved
    converter.Options.WebElementsMappingOptions.CssSelectors =
        elementsList.Split(new char[] { ',' });

    // create a new pdf document converting an url
    PdfDocument doc = converter.ConvertUrl(url);

    // get all web elements and mark/highlight them with a green rectangle
    foreach (WebElement element in 
        converter.Options.WebElementsMappingOptions.Result)
    {
        // each web element has several rendering rectangles 
        // (if it is displayed in more than 1 page)
        foreach (WebElementPdfRectangle webElementRectangle 
            in element.PdfRectangles)
        {
            // get the PDF page
            PdfPage page = doc.Pages[webElementRectangle.PageIndex];
            RectangleF rectangle = webElementRectangle.Rectangle;

            // highlight the HTML element
            PdfRectangleElement blueRectangle = new PdfRectangleElement(
                rectangle.X, rectangle.Y,
                rectangle.Width, rectangle.Height);
            blueRectangle.ForeColor = new PdfColor(0, 0, 255);

            page.Add(blueRectangle);
        }
    }

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
        	' instantiate a html to pdf converter object
	        Dim converter As New HtmlToPdf()
	
	        ' set the css selectors for the web elements
	        ' whose positions will be retrieved
	        converter.Options.WebElementsMappingOptions.CssSelectors = _
	            elementsList.Split(New Char() {","c})
	
	        ' create a new pdf document converting an url
	        Dim doc As PdfDocument = converter.ConvertUrl(url)
	
	        ' get all web elements and mark/highlight them with a green rectangle
	        For Each element As WebElement In _
	            converter.Options.WebElementsMappingOptions.Result
	            ' each web element has several rendering rectangles 
	            ' (if it is displayed in more than 1 page)
	            For Each webElementRectangle As WebElementPdfRectangle In _
	                element.PdfRectangles
	                ' get the PDF page
	                Dim page As PdfPage = doc.Pages(webElementRectangle.PageIndex)
	                Dim rectangle As RectangleF = webElementRectangle.Rectangle
	
	                ' highlight the HTML element
	                Dim blueRectangle As New PdfRectangleElement( _
	                    rectangle.X, rectangle.Y, rectangle.Width, rectangle.Height)
	                blueRectangle.ForeColor = New PdfColor(0, 0, 255)
	
	                page.Add(blueRectangle)
	            Next
	        Next
	
	        ' save pdf document
	        doc.Save("Sample.pdf")
	
	        ' close pdf document
	        doc.Close()
```

## Web page authentication

Select.Pdf HTML to PDF Converter can convert any web page a browser can open, however some web pages may require authentication before access can be granted. In such cases additional information must be supplied to the converter so that the converter can login to be granted access to the page.

#### HTTP Authentication

HTTP authentication is handled on the HTTP protocol level. When a page that requires HTTP authentication is accessed, the browser will display a login dialog where the user can enter an username and password. Once the correct user name and password is entered, access is granted and the page is displayed.

Select.Pdf HTML to PDF converter can automatically access pages that require HTTP authentication. To enable this feature, the `HtmlToPdfAuthenticationOptions.Username` and `HtmlToPdfAuthenticationOptions.Password` properties of `HtmlToPdfAuthenticationOptions` object need to be set with a valid user name and password for the page. Select.Pdf HTML to PDF converter will automatically use this information to login and access the page.

The following sample code shows how to convert an url that requires HTTP authentication:

```csharp
			        	// instantiate a html to pdf converter object
			            HtmlToPdf converter = new HtmlToPdf();
			
			            // set authentication options
			            converter.Options.Authentication.Username = username;
			            converter.Options.Authentication.Password = password;
			
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
			
			            ' set authentication options
			            converter.Options.Authentication.Username = username
			            converter.Options.Authentication.Password = password
			
			            ' create a new pdf document converting an url
			            Dim doc As PdfDocument = converter.ConvertUrl(url)
			
			            ' save pdf document
			            doc.Save("Sample.pdf")
			
			            ' close pdf document
			            doc.Close()
```

#### Integrated Windows Authentication

Select.Pdf HTML to PDF supports Integrated Windows Authentication if the NTML provider is used. The current user credentials will be used by the converter to authenticate automatically.

If the automatic windows authentication does not work and the converter cannot resolve the authentication using the current user credentials, the `HtmlToPdfAuthenticationOptions` object can be used to specify an username and a valid password, like in the above HTTP Authentication section.

#### Cookie (Forms) Authentication

Select.Pdf Library for .NET can convert web pages that authenticate the user with a cookie - ASP.NET Core cookie authentication, or ASP.NET Forms Authentication on the .NET Framework.

The converter issues its own request to the page, so it is anonymous unless it is told otherwise. The authentication cookie of the signed in user therefore has to be sent by the converter to the page that will be converted, through `HtmlToPdfOptions.HttpCookies`.

The cookie name and value are read from the request the application is currently serving. In ASP.NET Core that is the cookie of the cookie authentication handler - *.AspNetCore.Cookies* by default, or the name configured on its options - read through *Request.Cookies*. On the .NET Framework it is *FormsAuthentication.FormsCookieName*.

This sample code shows how to convert an url that requires cookie based authentication from a page of the same application:

```csharp
			        	// instantiate a html to pdf converter object
			            HtmlToPdf converter = new HtmlToPdf();

			            // send the application's authentication cookie to the page being converted,
			            // so the converter is seen by it as the already signed in user
			            converter.Options.HttpCookies.Add(authCookieName, authCookieValue);

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

			            ' send the application's authentication cookie to the page being converted,
			            ' so the converter is seen by it as the already signed in user
			            converter.Options.HttpCookies.Add(authCookieName, authCookieValue)

			            ' create a new pdf document converting an url
			            Dim doc As PdfDocument = converter.ConvertUrl(url)

			            ' save pdf document
			            doc.Save("Sample.pdf")

			            ' close pdf document
			            doc.Close()
```

#### Custom Login Page Authentication

In case the web page to be converted is protected by a general login mechanism, the Select.Pdf Html To Pdf Converter will not be able to access the page. To work around this problem, instead of converting the url directly, the page html code should be retrieve using some other method and that html code should be converted to pdf instead of the full url.

The simplest way is to let the application render the page to html itself and convert that string - the same technique described in "Converting the current page" (html-to-pdf.md). Alternatively, the converter can sign in on its own: see **Custom Login Page Authentication with Login Options** below, which needs no code in the application being converted.

IMPORTANT: If external resources (such as javascript/css files or images) are referenced in the html code, they must be placed into a location where anonymous access is granted, otherwise they will have the same authentication problems and they will not be rendered.

Once the application has the html of the page, the conversion is the same as any other html string conversion:

```csharp
			        	// instantiate a html to pdf converter object
			            HtmlToPdf converter = new HtmlToPdf();

			            // create a new pdf document converting the html the application rendered
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

			            ' create a new pdf document converting the html the application rendered
			            ' (baseUrl resolves the relative resources referenced by that html)
			            Dim doc As PdfDocument = converter.ConvertHtmlString(htmlString, baseUrl)

			            ' save pdf document
			            doc.Save("Sample.pdf")

			            ' close pdf document
			            doc.Close()
```

#### Custom Login Page Authentication with Login Options

In case the web page to be converted is protected by a general login mechanism, the SelectPdf Html To Pdf Converter can get past the login page if correct username/password and page fields are provided.

This sample code shows how to get past the login page of Facebook:

```csharp
			            string url = @"https://www.facebook.com";
			            
			        	// instantiate a html to pdf converter object
			            HtmlToPdf converter = new HtmlToPdf();
			
						// set login parameters
			            converter.Options.LoginOptions.LoginPage = @"https://www.facebook.com";
			            converter.Options.LoginOptions.UsernameFieldSelector = @"#email";
			            converter.Options.LoginOptions.PasswordFieldSelector = @"#pass";
			            converter.Options.LoginOptions.SubmitButtonSelector = @"#u_0_2";
			            converter.Options.LoginOptions.Username = @"PUT YOUR USERNAME HERE";
			            converter.Options.LoginOptions.Password = @"PUT YOUR PASSWORD HERE";
			            converter.Options.LoginOptions.DelayBefore = 1000;
			            converter.Options.LoginOptions.DelayAfter = 2000;
    
			            // create a new pdf document converting an url
			            PdfDocument doc = converter.ConvertUrl(url);
			
			            // save pdf document
			            doc.Save("Sample.pdf");
			
			            // close pdf document
			            doc.Close();
```

```vbnet
			        	Dim url As String = "https://www.facebook.com"
			        	
			        	' instantiate a html to pdf converter object
			            Dim converter As New HtmlToPdf()
			
					    ' set login parameters
					    converter.Options.LoginOptions.LoginPage = "https://www.facebook.com"
					    converter.Options.LoginOptions.UsernameFieldSelector = "#email"
					    converter.Options.LoginOptions.PasswordFieldSelector = "#pass"
					    converter.Options.LoginOptions.SubmitButtonSelector = "#u_0_2"
					    converter.Options.LoginOptions.Username = "PUT YOUR USERNAME HERE"
					    converter.Options.LoginOptions.Password = "PUT YOUR PASSWORD HERE"
					    converter.Options.LoginOptions.DelayBefore = 1000
					    converter.Options.LoginOptions.DelayAfter = 2000
			
			            ' create a new pdf document converting an url
			            Dim doc As PdfDocument = converter.ConvertUrl(url)
			
			            ' save pdf document
			            doc.Save("Sample.pdf")
			
			            ' close pdf document
			            doc.Close()
```

## HTTP cookies

Select.Pdf Library for .NET can be used to send additional HTTP cookies to the web page that is converted.

This can be done using the `HtmlToPdfOptions.HttpCookies` property of the `HtmlToPdfOptions` object.

### Sample Code

This sample code shows how to send HTTP cookies to the page that will be converted using the html to pdf converter from Select.Pdf Pdf Library for .NET.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set the HTTP cookies
    converter.Options.HttpCookies.Add(name1, value1);
    converter.Options.HttpCookies.Add(name2, value2);
    converter.Options.HttpCookies.Add(name3, value3);
    converter.Options.HttpCookies.Add(name4, value4);

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

    ' set the HTTP cookies
    converter.Options.HttpCookies.Add(name1, value1)
    converter.Options.HttpCookies.Add(name2, value2)
    converter.Options.HttpCookies.Add(name3, value3)
    converter.Options.HttpCookies.Add(name4, value4)

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## HTTP headers

Select.Pdf Library for .NET can be used to send additional HTTP headers to the web page that is converted.

This can be done using the `HtmlToPdfOptions.HttpHeaders` property of the `HtmlToPdfOptions` object.

### Sample Code

This sample code shows how to send HTTP headers to the page that will be converted using the html to pdf converter from Select.Pdf Pdf Library for .NET.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set the HTTP headers
    converter.Options.HttpHeaders.Add(name1, value1);
    converter.Options.HttpHeaders.Add(name2, value2);
    converter.Options.HttpHeaders.Add(name3, value3);
    converter.Options.HttpHeaders.Add(name4, value4);

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

    ' set the HTTP headers
    converter.Options.HttpHeaders.Add(name1, value1)
    converter.Options.HttpHeaders.Add(name2, value2)
    converter.Options.HttpHeaders.Add(name3, value3)
    converter.Options.HttpHeaders.Add(name4, value4)

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## HTTP POST requests

By default, when a page is converted to PDF, SelectPdf performs a GET request for that page to obtain its content. SelectPdf Library for .NET can be used to send additional parameters to the web page that is converted using a HTTP POST request.

This can be done using the `HtmlToPdfOptions.HttpPostParameters` property of the `HtmlToPdfOptions` object.

### Sample Code

This sample shows how to send parameters using a HTTP POST request to the page that will be converted using the html to pdf converter from SelectPdf Pdf Library for .NET.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set the HTTP POST parameters
    converter.Options.HttpPostParameters.Add(name1, value1);
    converter.Options.HttpPostParameters.Add(name2, value2);
    converter.Options.HttpPostParameters.Add(name3, value3);
    converter.Options.HttpPostParameters.Add(name4, value4);

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

            ' set the HTTP POST parameters
	        converter.Options.HttpPostParameters.Add(name1, value1)
	        converter.Options.HttpPostParameters.Add(name2, value2)
	        converter.Options.HttpPostParameters.Add(name3, value3)
	        converter.Options.HttpPostParameters.Add(name4, value4)

            ' create a new pdf document converting an url
            Dim doc As PdfDocument = converter.ConvertUrl(url)

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

## Proxy servers

Sometime a system is setup to be able to access the internet through a proxy. Select.Pdf Html to Pdf Converter supports this option and can convert web pages that can be accessed only though a proxy server.

To enable this feature the `HtmlToPdfOptions.ProxyOptions` property of the `HtmlToPdfOptions` object needs to be set. This is an instance of `HtmlToPdfProxyOptions` class that allows to set:

- Proxy Host Name
- Proxy Port Number
- Username
- Password
- Proxy Type

### Sample Code

This sample code shows how to convert an url to pdf using Select.Pdf Pdf Library for .NET and use a proxy to access the web page.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set the proxy options
    converter.Options.ProxyOptions.HostName = "proxy.server.name";
    converter.Options.ProxyOptions.PortNumber = 1122;
    converter.Options.ProxyOptions.Username = "proxyuser";
    converter.Options.ProxyOptions.Password = "proxypassword";
    converter.Options.ProxyOptions.Type = NetworkProxyType.Http;

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

    ' set the proxy options
    converter.Options.ProxyOptions.HostName = "proxy.server.name"
    converter.Options.ProxyOptions.PortNumber = 1122
    converter.Options.ProxyOptions.Username = "proxyuser"
    converter.Options.ProxyOptions.Password = "proxypassword"
    converter.Options.ProxyOptions.Type = NetworkProxyType.Http

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```
