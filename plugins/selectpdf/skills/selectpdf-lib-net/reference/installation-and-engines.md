# Installation and rendering engines

How to install the commercial Select.Pdf .NET library, set the license key, write a first conversion, and pick one of its four HTML rendering engines.

## NuGet packages

Install exactly one **main library** package, chosen by target framework and bitness: `Select.Pdf.NetCore` (modern .NET — .NET Framework 4.6.1 / 4.7.2, .NET Core 2.0+ through .NET Standard 2.0, .NET 5 – .NET 10, AnyCPU) or its `Select.Pdf.NetCore.x64` variant; or `Select.Pdf` (legacy .NET Framework 2.0 – 4.5, AnyCPU) or its `Select.Pdf.x64` variant. The default WebKit, WebKit Restricted, and (on supported targets) Blink engines need no extra package. To use Blink, also install the matching companion: `Select.Pdf.NetCore.Blink` (for `Select.Pdf.NetCore`) or `Select.Pdf.NetCore.Blink.x64` (for `Select.Pdf.NetCore.x64`) — Blink companions are modern-.NET only. To use Chromium, install the matching optional runtime package: `Select.Pdf.NetCore.Chromium.Windows` / `Select.Pdf.NetCore.Chromium.Windows.x64` (companions for the NetCore packages) or `Select.Pdf.Chromium.Windows` / `Select.Pdf.Chromium.Windows.x64` (companions for the legacy Select.Pdf packages); the Chromium.Windows package deploys the Chromium runtime into the build output automatically and declares the main package as a direct dependency. An optional `Select.Pdf.Extras` / `Select.Pdf.Extras.x64` add-on contains extra features (new PDF forms filling engine, PDF compressor).

## Supported platforms and frameworks

The library runs on **Windows only** (not Linux, macOS, or Xamarin) and requires **full trust** because it makes native API calls. The `.NetCore` packages cover .NET Framework 4.6.1 / 4.7.2, .NET Core 2.0+ through .NET Standard 2.0, and .NET 5 – .NET 10; the legacy `Select.Pdf` packages cover .NET Framework 2.0 – 4.5. Both AnyCPU and x64 variants exist — if an `.x64` package is used, the application must target x64 and run in an x64 worker process.

## Setting the license key

Set the license key once via the static `GlobalProperties.LicenseKey` property before performing any conversion. Without a license key, the library runs in **trial mode**.

```csharp
GlobalProperties.LicenseKey = "your-license-key";
```

```vbnet
GlobalProperties.LicenseKey = "your-license-key"
```

## Getting started

Import the `SelectPdf` namespace, create a `PdfDocument`, add a `PdfPage`, add content (e.g. a `PdfTextElement` using a `PdfFont` from `doc.AddFont`), then `Save` and `Close` the document. `PdfDocument.Save` has overloads for a file path, a stream, or an HTTP response; `Close` releases resources.

```csharp
using SelectPdf;

// create a new pdf document
PdfDocument doc = new PdfDocument();

// add a new page to the document
PdfPage page = doc.AddPage();

// create a new pdf font
PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 20;

// create a new text element and add it to the page
PdfTextElement text = new PdfTextElement(50, 50, "Hello world!", font);
page.Add(text);

// save and close
doc.Save("output.pdf");
doc.Close();
```

```vbnet
Imports SelectPdf

' create a new pdf document
Dim doc As New PdfDocument()

' add a new page to the document
Dim page As PdfPage = doc.AddPage()

' create a new pdf font
Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 20

' create a new text element and add it to the page
Dim text As New PdfTextElement(50, 50, "Hello world!", font)
page.Add(text)

' save and close
doc.Save("output.pdf")
doc.Close()
```

## Choosing a rendering engine

The HTML-to-PDF converter exposes four engines through the same `HtmlToPdf` API; switch with the one-line `HtmlToPdfOptions.RenderingEngine` property (`RenderingEngine` enum). **WebKit** (default) is available on every supported target and renders ES5 JavaScript and classic CSS — start here. **WebKitRestricted** is a simplified WebKit for constrained environments such as Azure Web Apps (same ES5 footprint, some features disabled). **Blink** is the Chrome engine for modern HTML5/CSS3 and ES6, available only on .NET Core 2.0+ and .NET Framework 4.6.1+, and requires a Blink companion package. **Chromium** (CEF backend, introduced v26.2) renders modern HTML5/CSS3 and ES2020+, is available for every supported .NET target including legacy .NET Framework, runs on Azure Web Apps, and requires a Chromium.Windows runtime package. Note: partial web-page rendering via `VisibleWebElementId` is supported only by the WebKit engines; tagged/PDF-UA and PDF/A-3a output require Blink or Chromium.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set converter rendering engine
converter.Options.RenderingEngine = RenderingEngine.WebKit;
// or: RenderingEngine.WebKitRestricted
// or: RenderingEngine.Blink
// or: RenderingEngine.Chromium

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

' set converter rendering engine
converter.Options.RenderingEngine = RenderingEngine.WebKit
' or: RenderingEngine.WebKitRestricted
' or: RenderingEngine.Blink
' or: RenderingEngine.Chromium

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document
doc.Save("Sample.pdf")

' close pdf document
doc.Close()
```

## Chromium engine specifics

The Chromium engine uses a **Chromium Embedded Framework (CEF)** backend shipped in a separate `Chromium.Windows` package; select it by setting `RenderingEngine.Chromium`. The folder containing the CEF runtime can be specified explicitly via `HtmlToPdfOptions.CefEnginePath` (the folder holding `SelectPdf.Cef.exe`). The Blink engine's binary location is set analogously via `HtmlToPdfOptions.BlinkEnginePath` (the folder holding Blink's `chrome.exe`). Both engines can write a native engine log to a path given by `HtmlToPdfOptions.LFN` (useful for Azure diagnostics).

```csharp
HtmlToPdf converter = new HtmlToPdf();

// select the Chromium rendering engine
converter.Options.RenderingEngine = RenderingEngine.Chromium;

// optional: explicit engine binary paths
converter.Options.CefEnginePath = @"C:\app\cef";    // folder with SelectPdf.Cef.exe
// converter.Options.BlinkEnginePath = @"C:\app\blink"; // folder with chrome.exe (Blink)

// standard conversion options can still be used
converter.Options.PdfPageSize = PdfPageSize.A4;
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait;
converter.Options.MarginTop = 20;
converter.Options.MarginBottom = 20;

PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");

try
{
    doc.Save("ChromiumSample.pdf");
}
finally
{
    doc.Close();
}
```

```vbnet
Dim converter As New HtmlToPdf()

' select the Chromium rendering engine
converter.Options.RenderingEngine = RenderingEngine.Chromium

' optional: explicit engine binary paths
converter.Options.CefEnginePath = "C:\app\cef"      ' folder with SelectPdf.Cef.exe
' converter.Options.BlinkEnginePath = "C:\app\blink"  ' folder with chrome.exe (Blink)

' standard conversion options can still be used
converter.Options.PdfPageSize = PdfPageSize.A4
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait
converter.Options.MarginTop = 20
converter.Options.MarginBottom = 20

Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")

Try
    doc.Save("ChromiumSample.pdf")
Finally
    doc.Close()
End Try
```

## Deployment basics

A standard deployment needs three files: `Select.Pdf.dll`, `Select.Html.dep`, and `Select.Tools.dep` — no additional server or client configuration. The deployment machine must grant **full trust** (the library makes native API calls); on shared hosting confirm full trust is available, otherwise move to a dedicated/virtual server. If the Blink engine is used, deploy its full Chromium binaries folder as well; if the Chromium engine is used, deploy the runtime folder installed by the matching `Chromium.Windows` package (MSBuild places it in the output directory automatically, so a normal publish picks it up). For GAC installs only `Select.Pdf.dll` is added — the `.dep` files are copied as linked resources. On Azure, the WebKit Restricted and Chromium engines run on Azure Web Apps (Basic plan or above); classic Azure WebSite execution mode is otherwise unsupported, so deploy to a Cloud Service or Virtual Machine when not using those engines.

See also: html-to-pdf.md, deployment-azure-troubleshooting.md, api-cheatsheet.md
