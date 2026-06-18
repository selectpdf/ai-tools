# Authentication, cookies, HTTP headers, POST and proxy (Community Edition)

How to make the free Community Edition (`Select.HtmlToPdf`) `HtmlToPdf` converter reach pages that require login, custom cookies, custom headers, a POST body, or a proxy — all configured through `converter.Options`.

## Web page authentication

Some web pages require authentication before access is granted. For HTTP authentication, set the `Username` and `Password` properties of `HtmlToPdfOptions.Authentication` (`HtmlToPdfAuthenticationOptions`) via `converter.Options.Authentication`; the converter then logs in automatically before fetching the page. The same `Authentication` options also cover Integrated Windows Authentication when the NTLM provider is used.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set authentication options
converter.Options.Authentication.Username = username;
converter.Options.Authentication.Password = password;

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

' set authentication options
converter.Options.Authentication.Username = username
converter.Options.Authentication.Password = password

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## HTTP cookies

You can send additional HTTP cookies to the page being converted through the `HttpCookies` property of `HtmlToPdfOptions`, accessed as `converter.Options.HttpCookies`. Add each cookie as a name/value pair with `HttpCookies.Add(name, value)`.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set the HTTP cookies
converter.Options.HttpCookies.Add(TxtName1.Text, TxtValue1.Text);
converter.Options.HttpCookies.Add(TxtName2.Text, TxtValue2.Text);
converter.Options.HttpCookies.Add(TxtName3.Text, TxtValue3.Text);
converter.Options.HttpCookies.Add(TxtName4.Text, TxtValue4.Text);

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

' set the HTTP cookies
converter.Options.HttpCookies.Add(TxtName1.Text, TxtValue1.Text)
converter.Options.HttpCookies.Add(TxtName2.Text, TxtValue2.Text)
converter.Options.HttpCookies.Add(TxtName3.Text, TxtValue3.Text)
converter.Options.HttpCookies.Add(TxtName4.Text, TxtValue4.Text)

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl.Text)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Custom HTTP headers

You can send additional HTTP headers to the page being converted through the `HttpHeaders` property of `HtmlToPdfOptions`, accessed as `converter.Options.HttpHeaders`. Add each header as a name/value pair with `HttpHeaders.Add(name, value)`.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set the HTTP headers
converter.Options.HttpHeaders.Add(TxtName1.Text, TxtValue1.Text);
converter.Options.HttpHeaders.Add(TxtName2.Text, TxtValue2.Text);
converter.Options.HttpHeaders.Add(TxtName3.Text, TxtValue3.Text);
converter.Options.HttpHeaders.Add(TxtName4.Text, TxtValue4.Text);

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

' set the HTTP headers
converter.Options.HttpHeaders.Add(TxtName1.Text, TxtValue1.Text)
converter.Options.HttpHeaders.Add(TxtName2.Text, TxtValue2.Text)
converter.Options.HttpHeaders.Add(TxtName3.Text, TxtValue3.Text)
converter.Options.HttpHeaders.Add(TxtName4.Text, TxtValue4.Text)

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl.Text)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## HTTP POST request

By default the converter performs a GET request for the page. To send parameters via a HTTP POST request instead, use the `HttpPostParameters` property of `HtmlToPdfOptions`, accessed as `converter.Options.HttpPostParameters`. Add each parameter as a name/value pair with `HttpPostParameters.Add(name, value)`.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set the HTTP POST parameters
converter.Options.HttpPostParameters.Add(TxtName1.Text, TxtValue1.Text);
converter.Options.HttpPostParameters.Add(TxtName2.Text, TxtValue2.Text);
converter.Options.HttpPostParameters.Add(TxtName3.Text, TxtValue3.Text);
converter.Options.HttpPostParameters.Add(TxtName4.Text, TxtValue4.Text);

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

' set the HTTP POST parameters
converter.Options.HttpPostParameters.Add(TxtName1.Text, TxtValue1.Text)
converter.Options.HttpPostParameters.Add(TxtName2.Text, TxtValue2.Text)
converter.Options.HttpPostParameters.Add(TxtName3.Text, TxtValue3.Text)
converter.Options.HttpPostParameters.Add(TxtName4.Text, TxtValue4.Text)

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(TxtUrl.Text)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

## Proxy options

To convert web pages that are reachable only through a proxy server, set the `ProxyOptions` property of `HtmlToPdfOptions` (an `HtmlToPdfProxyOptions` instance), accessed as `converter.Options.ProxyOptions`. It exposes `HostName`, `PortNumber`, `Username`, `Password`, and `Type` (a `NetworkProxyType`, e.g. `NetworkProxyType.Http`).

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set proxy options
converter.Options.ProxyOptions.HostName = "proxy.server.name";
converter.Options.ProxyOptions.PortNumber = 1122;
converter.Options.ProxyOptions.Username = "proxyuser";
converter.Options.ProxyOptions.Password = "proxypassword";
converter.Options.ProxyOptions.Type = NetworkProxyType.Http;

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

' set proxy options
converter.Options.ProxyOptions.HostName = "proxy.server.name"
converter.Options.ProxyOptions.PortNumber = 1122
converter.Options.ProxyOptions.Username = "proxyuser"
converter.Options.ProxyOptions.Password = "proxypassword"
converter.Options.ProxyOptions.Type = NetworkProxyType.Http

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("output.pdf")

' close pdf document
doc.Close()
```

See also: html-to-pdf.md, installation-and-engines.md
