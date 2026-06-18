# Web element mapping, authentication, cookies, headers, POST and proxy

Advanced `HtmlToPdf` conversion features in the commercial Select.Pdf .NET library: mapping HTML elements to their PDF coordinates, and supplying authentication, cookies, headers, POST parameters, and proxy settings to the page being converted.

## Mapping HTML elements to their PDF location

The converter can locate specified HTML elements in the generated PDF. Set the elements to track via `converter.Options.WebElementsMappingOptions.CssSelectors` (a `string[]` of CSS selectors — e.g. `"H1"`, `"*.myclass"`, `"*#myid"`). After conversion, `WebElementsMappingOptions.Result` holds the mapping as a collection of `WebElement` objects; each `WebElement` exposes `PdfRectangles`, a collection of `WebElementPdfRectangle` whose `.PageIndex` gives the PDF page and `.Rectangle` gives a `System.Drawing.RectangleF` (X/Y/Width/Height). An element has more than one rectangle when it spans multiple pages. This is useful for drawing links, overlays, or bookmarks over rendered elements.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// set the css selectors for the web elements whose positions will be retrieved
converter.Options.WebElementsMappingOptions.CssSelectors =
    TxtElements.Split(new char[] { ',' });

PdfDocument doc = converter.ConvertUrl(TxtUrl);

// get all web elements and mark/highlight them with a blue rectangle
foreach (WebElement element in converter.Options.WebElementsMappingOptions.Result)
{
    // each web element has several rendering rectangles (if displayed on >1 page)
    foreach (WebElementPdfRectangle webElementRectangle in element.PdfRectangles)
    {
        PdfPage page = doc.Pages[webElementRectangle.PageIndex];
        RectangleF rectangle = webElementRectangle.Rectangle;

        PdfRectangleElement blueRectangle = new PdfRectangleElement(
            rectangle.X, rectangle.Y, rectangle.Width, rectangle.Height);
        blueRectangle.ForeColor = Color.Blue;

        page.Add(blueRectangle);
    }
}

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' set the css selectors for the web elements whose positions will be retrieved
converter.Options.WebElementsMappingOptions.CssSelectors = _
    TxtElements.Split(New Char() {","c})

Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl)

' get all web elements and mark/highlight them with a blue rectangle
For Each element As WebElement In converter.Options.WebElementsMappingOptions.Result
    ' each web element has several rendering rectangles (if displayed on >1 page)
    For Each webElementRectangle As WebElementPdfRectangle In element.PdfRectangles
        Dim page As PdfPage = doc.Pages(webElementRectangle.PageIndex)
        Dim rectangle As RectangleF = webElementRectangle.Rectangle

        Dim blueRectangle As New PdfRectangleElement( _
            rectangle.X, rectangle.Y, rectangle.Width, rectangle.Height)
        blueRectangle.ForeColor = Color.Blue

        page.Add(blueRectangle)
    Next
Next

doc.Save("output.pdf")
doc.Close()
```

## Web page authentication

Some pages require a login before they can be rendered. For HTTP (and integrated Windows / NTLM) authentication, set `converter.Options.Authentication.Username` and `converter.Options.Authentication.Password` (a `HtmlToPdfAuthenticationOptions` object); the converter logs in automatically. For ASP.NET Forms authentication, send the forms-authentication cookie instead via the `HttpCookies` collection (see below). For pages behind a custom login page, retrieve the HTML out-of-band and convert it with `ConvertHtmlString`.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// set authentication options
converter.Options.Authentication.Username = username;
converter.Options.Authentication.Password = password;

PdfDocument doc = converter.ConvertUrl(url);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' set authentication options
converter.Options.Authentication.Username = username
converter.Options.Authentication.Password = password

Dim doc As PdfDocument = converter.ConvertUrl(url)

doc.Save("output.pdf")
doc.Close()
```

## HTTP cookies

Send additional cookies to the converted page through the `converter.Options.HttpCookies` collection, adding each cookie with `.Add(name, value)`.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// set the HTTP cookies
converter.Options.HttpCookies.Add(name1, value1);
converter.Options.HttpCookies.Add(name2, value2);

PdfDocument doc = converter.ConvertUrl(url);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' set the HTTP cookies
converter.Options.HttpCookies.Add(name1, value1)
converter.Options.HttpCookies.Add(name2, value2)

Dim doc As PdfDocument = converter.ConvertUrl(url)

doc.Save("output.pdf")
doc.Close()
```

## Custom HTTP headers

Send additional request headers to the converted page through the `converter.Options.HttpHeaders` collection, adding each header with `.Add(name, value)`.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// set the HTTP headers
converter.Options.HttpHeaders.Add(name1, value1);
converter.Options.HttpHeaders.Add(name2, value2);

PdfDocument doc = converter.ConvertUrl(url);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' set the HTTP headers
converter.Options.HttpHeaders.Add(name1, value1)
converter.Options.HttpHeaders.Add(name2, value2)

Dim doc As PdfDocument = converter.ConvertUrl(url)

doc.Save("output.pdf")
doc.Close()
```

## HTTP POST request

By default the converter performs a GET for the page. To send parameters with a POST request instead, add them to the `converter.Options.HttpPostParameters` collection with `.Add(name, value)`.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// set the HTTP POST parameters
converter.Options.HttpPostParameters.Add(name1, value1);
converter.Options.HttpPostParameters.Add(name2, value2);

PdfDocument doc = converter.ConvertUrl(url);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' set the HTTP POST parameters
converter.Options.HttpPostParameters.Add(name1, value1)
converter.Options.HttpPostParameters.Add(name2, value2)

Dim doc As PdfDocument = converter.ConvertUrl(url)

doc.Save("output.pdf")
doc.Close()
```

## Proxy options

To convert pages reachable only through a proxy, set `converter.Options.ProxyOptions` (a `HtmlToPdfProxyOptions` object): `HostName`, `PortNumber`, `Username`, `Password`, and `Type` (a `NetworkProxyType`, e.g. `NetworkProxyType.Http`).

```csharp
HtmlToPdf converter = new HtmlToPdf();

// set the proxy options
converter.Options.ProxyOptions.HostName = "proxy.server.name";
converter.Options.ProxyOptions.PortNumber = 1122;
converter.Options.ProxyOptions.Username = "proxyuser";
converter.Options.ProxyOptions.Password = "proxypassword";
converter.Options.ProxyOptions.Type = NetworkProxyType.Http;

PdfDocument doc = converter.ConvertUrl(TxtUrl);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' set the proxy options
converter.Options.ProxyOptions.HostName = "proxy.server.name"
converter.Options.ProxyOptions.PortNumber = 1122
converter.Options.ProxyOptions.Username = "proxyuser"
converter.Options.ProxyOptions.Password = "proxypassword"
converter.Options.ProxyOptions.Type = NetworkProxyType.Http

Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl)

doc.Save("output.pdf")
doc.Close()
```

See also: html-to-pdf.md, links-bookmarks-toc.md, api-cheatsheet.md
