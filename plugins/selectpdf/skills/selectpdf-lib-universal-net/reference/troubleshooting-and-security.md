# Troubleshooting and security recommendations

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## Troubleshooting

### Conversion failure. The rendering engine could not be found.

The HTML-to-PDF conversion needs the native Chromium (CEF) engine folder (**Chromium-CEF-154.0.28**) and, for the PDF-to-text / PDF-to-image converters, the native PDF tool (**SelectPdf.Tools.dep** on Windows, **SelectPdf.Tools** on Linux) next to the application. These are supplied by the **SelectPdf.Universal.Native.<rid>** package and are copied to the output folder at build time. If they are missing at run time, confirm the correct Native package (matching your deployment runtime identifier) is installed and published. The engine folder path can also be set explicitly with `HtmlToPdfOptions.ChromiumEnginePath`. More details in the deployment section.

### Conversion failure. Exception of type 'System.OutOfMemoryException' was thrown.

This error might occur in case of html to pdf conversion of large web pages, containing many / very large images.

Our tool initially converts the images to bitmaps and then compresses them to jpg internally. If you have several large images (with large resolutions), even if you display them in a 20x30 box in html, they are still large and when converted to bmp, they consume a lot of memory.

To work around this problem, you need to have your images optimized for the web. Alternatively, run the application as a 64-bit process, which allows the allocation of a lot more memory than a 32-bit process.

### Conversion failure error 5.

The error code is this:

ERROR_ACCESS_DENIED

5 (0x5)

Access is denied.

The engine binary needs execute permission. On Linux the library restores the execute bit automatically before launching the engine, but if the application directory is mounted noexec, or on a host that blocks launching the engine, grant execute permission to the engine binary (the CEF executable inside the **Chromium-CEF-154.0.28** folder and **SelectPdf.Tools**).

### Conversion failure error 32.

The error code is this:

ERROR_SHARING_VIOLATION

32 (0x20)

The process cannot access the file because it is being used by another process.

Probably something happened during application files transfer and an engine file remained locked. Try to redeploy it.

### Conversion failure error 1260.

The error code is this:

ERROR_ACCESS_DISABLED_BY_POLICY

1260 (0x4EC)

This program is blocked by group policy. For more information, contact your system administrator.

Our tool launches a new process that renders the html. The application needs enough rights to do that.

### During html to pdf conversion, web fonts sometimes do not appear or the text renders incorrectly.

This is usually a timing issue - the web fonts have not finished loading when the page is captured. Add a small delay before the conversion to allow the page more time to load its fonts:

```csharp
			converter.Options.ConversionDelay = 2;
```

```vbnet
			converter.Options.ConversionDelay = 2
```

### Issue with web fonts on HTML to PDF conversion.

The Chromium engine loads and embeds the web fonts referenced by the page in the standard web font formats (WOFF2, WOFF, TTF, OTF). Make sure the font files are reachable from the page and that the page has time to load them (see the previous section about adding a load delay).

### When a html string is converted to pdf, the styles are not applied and the images are missing.

When you convert a string to pdf and have references to external resources, the converter needs to know the location of the html. To do that you need to use the version of ConvertHtmlString method has also an additional parameter called baseUrl. Use that to specify the path where the file should be. Using it and the relative path to the image or css or js file from html, the converter will be able to calculate the full path to the resource file. `HtmlToPdf.ConvertHtmlString(string, string)` contains an additional parameter: *baseUrl*. The *baseUrl* parameter allows the converter to resolve relative urls. Basically, *baseUrl + relative image/css url = full absolute url*. An http or https *baseUrl* is also the page's own address and origin, so a web font, a fetch request or a module script on the same site needs no CORS headers - the page is served as the response for that url, not from a temporary file.

### I convert a web page to PDF and one of the images gets split between 2 pages. How do I make it stay in one page?

There are 2 solutions for this problem:

Solution 1: Put the CSS **break-inside: avoid** on the image, or on the element that contains it. The rendering engine paginates the page itself and honors that rule, so the image is moved whole to the next pdf page rather than being split. Applied to a single element it affects only that one; applied to **img** it keeps every image on the page together, which can leave large gaps where an image is nearly as tall as the page.

Solution 2: Modify the html and set the style *page-break-inside: avoid* on the img tag of the image that you need to stay in one page.

The "page-break-inside" property sets whether a page break is allowed INSIDE a specified element. The element can be anything (image, table, table row, div, text, etc).

```html
			<div style="page-break-inside: avoid">
			The content of this div element will not be split into several pages (if possible)
			because "page-break-inside" property is set to "avoid".
			</div>        	
```

### HTML to PDF. Conversion error: Navigation timeout.

The error message is self-explanatory and the error can be caused by multiple things:

1. The web page takes a long amount of time to load. The default amount of time that SelectPdf waits for a page is 60 seconds. If the page takes longer to load, try to increase the timeout using the property `HtmlToPdfOptions.NavigationTimeout` property of the `HtmlToPdfOptions` object:

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set the page timeout (in seconds)
    converter.Options.NavigationTimeout = 120;
```

```vbnet
	' instantiate a html to pdf converter object
    Dim converter As New HtmlToPdf()

    ' set the page timeout (in seconds)
    converter.Options.NavigationTimeout = 120
```

2. The url of the web page being converted is not resolved on the server. We've seen cases with servers that were not able to resolve urls of the domains hosted on the same machine. Connect remotely to your server and check to see if the url is resolved.

3. You are using a proxy server to connect to the internet. SelectPdf Html to Pdf Converter supports this option and can convert web pages that can be accessed only though a proxy server. Use `HtmlToPdfOptions.ProxyOptions` property of the `HtmlToPdfOptions` object to setup your proxy.

4. You have set the startup mode of the converter to Manual and the javascript trigger method was not called.

### HTML to PDF conversion fails on a minimal Linux server or container.

The Chromium engine renders headless (no X server or Xvfb is needed), but it depends on the usual Chromium system libraries. A desktop Linux distribution already has them; a minimal server or container image may not. Install the Chromium runtime dependencies (NSS, glib/gobject/gio, atk/at-spi, the X11 client libraries, gbm, cairo, pango, alsa, dbus, cups, expat, xkbcommon, udev), or place the bundled `.so` closure alongside the engine. The pure-managed features (from-scratch documents, merge, encryption) and the statically linked native PDF tool have no such dependency. See the deployment section.

## Security recommendations

SelectPdf is a .NET library that offers a lot of features, but the most important and the most used feature is the conversion from HTML to PDF. The conversion from HTML to PDF implies the rendering of the web page being converted. Because of the support to run complex HTML/CSS/Javascript, a number of things need to be taken into consideration when using SelectPdf, to make sure your application remains secure.

If you convert your own files to pdf and know exactly what they contain, you are fine. But if you take external files/code to convert to PDF from users you do not know, follow the following recommendations.

**Control input**

Check the content of the web page being converted. Look for code (especially javascript) that might harm your system. Sanitizing user input can be difficult and attackers can sometimes find a way around the protection.

**Retrict access**

If you only access the system from certain locations, restrict the access to it limiting the IP addresses allowed.

**Disable javascript execution**

If you do not need to execute javascript, disable it setting the following property:

```csharp
	converter.Options.JavaScriptEnabled = false;
```

```vbnet
	converter.Options.JavaScriptEnabled = False
```

**Disable access to local files**

If you do not need to access local server files during the conversion, disable access setting the following property:

```csharp
	SelectPdf.Universal.GlobalProperties.ForceDenyLocalFileAccess = true;
```

```vbnet
	SelectPdf.Universal.GlobalProperties.ForceDenyLocalFileAccess = True
```

**Isolate conversions of pages you do not trust**

By default the engine may run inside your application's process (`GlobalProperties.ChromiumEngineHostMode`). A page you do not trust can be converted in a separate engine process instead, so that a crash or a hang stays out of your application:

```csharp
	converter.Options.ForceOutOfProcess = true;
```

```vbnet
	converter.Options.ForceOutOfProcess = True
```

**Do not share the HTTP cache between users**

When the engine runs inside your application it keeps its HTTP cache from one conversion to the next by default (`HtmlToPdfOptions.HttpCache`), which makes repeated conversions faster. The cache is keyed by address, not by the user a conversion runs for, so a response one conversion fetched can be served to the next. If your application converts pages on behalf of different users whose content must stay separate, start every conversion with an empty cache - or keep only resources from other sites, which never includes the converted page's own responses:

```csharp
	converter.Options.HttpCache = HttpCacheMode.Clear;   // or HttpCacheMode.ThirdParty
```

```vbnet
	converter.Options.HttpCache = HttpCacheMode.Clear   ' or HttpCacheMode.ThirdParty
```
