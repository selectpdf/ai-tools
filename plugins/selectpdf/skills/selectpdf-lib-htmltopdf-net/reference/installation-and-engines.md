# Installation and rendering engines (Community Edition)

The SelectPdf HTML To Pdf Converter Community Edition (`Select.HtmlToPdf`) is a free, HTML-to-PDF-only .NET library that generates PDF documents of up to 5 pages from URLs and HTML code.

## NuGet packages

Install exactly one **main library** package, chosen by target framework, and optionally one **engine companion** package (Blink or Chromium) that matches it; the companion is only needed when the Blink or Chromium engine is used, not for the default WebKit engine. The main packages are `Select.HtmlToPdf.NetCore` (.NET Framework 4.6.1 / 4.7.2, .NET Core 2.0+ through .NET Standard 2.0, and .NET 5 – .NET 10, Windows only) and `Select.HtmlToPdf` (legacy .NET Framework 2.0 – 4.5). The Blink companion is `Select.HtmlToPdf.NetCore.Blink` (for `Select.HtmlToPdf.NetCore`). The Chromium companions are `Select.HtmlToPdf.NetCore.Chromium.Windows` (for `Select.HtmlToPdf.NetCore`) and `Select.HtmlToPdf.Chromium.Windows` (for `Select.HtmlToPdf`); each Chromium.Windows package ships the Windows Chromium runtime and declares the matching main package as a dependency. Install the Chromium.Windows package only when the Chromium engine is actually needed.

```csharp
// .NET (Core / 5+) and .NET Framework 4.6.1+:
//   dotnet add package Select.HtmlToPdf.NetCore
//   dotnet add package Select.HtmlToPdf.NetCore.Blink              (optional, Blink engine)
//   dotnet add package Select.HtmlToPdf.NetCore.Chromium.Windows  (optional, Chromium engine)
// Legacy .NET Framework 2.0 - 4.5:
//   dotnet add package Select.HtmlToPdf
//   dotnet add package Select.HtmlToPdf.Chromium.Windows           (optional, Chromium engine)
```

```vbnet
' .NET (Core / 5+) and .NET Framework 4.6.1+:
'   dotnet add package Select.HtmlToPdf.NetCore
'   dotnet add package Select.HtmlToPdf.NetCore.Blink              (optional, Blink engine)
'   dotnet add package Select.HtmlToPdf.NetCore.Chromium.Windows  (optional, Chromium engine)
' Legacy .NET Framework 2.0 - 4.5:
'   dotnet add package Select.HtmlToPdf
'   dotnet add package Select.HtmlToPdf.Chromium.Windows           (optional, Chromium engine)
```

## Supported platforms and frameworks

The library runs on Windows only; the `.NetCore` packages do not run on Linux, macOS, or Xamarin. `Select.HtmlToPdf.NetCore` covers .NET Framework 4.6.1 / 4.7.2, .NET Core 2.0+ through .NET Standard 2.0, and .NET 5 – .NET 10; `Select.HtmlToPdf` covers legacy .NET Framework 2.0 – 4.5. The Chromium.Windows packages are built AnyCPU and bundle the x86 Chromium runtime, so they support both x86 and x64 host processes. When installing from downloaded assemblies instead of NuGet, reference `Select.HtmlToPdf.dll` and place the companion `Select.Html.dep` file in the same folder (the bin directory).

## License key

The Community Edition is a free product usable for personal or commercial purposes without limitation, so no license key is required to run it. License-key activation belongs to the commercial Select.Pdf Library, not to the Community Edition; the `SelectPdf.GlobalProperties` class in the Community Edition exposes deployment paths such as `HtmlEngineFullPath` (the path to `Select.Html.dep`) rather than a license-key property. If you later upgrade to the commercial library, follow that product's licensing documentation for key setup.

```csharp
using SelectPdf;

// Community Edition is free - no license key needed.
// Optionally point GlobalProperties at the engine dependency file:
GlobalProperties.HtmlEngineFullPath = @"C:\app\bin\Select.Html.dep";
```

```vbnet
Imports SelectPdf

' Community Edition is free - no license key needed.
' Optionally point GlobalProperties at the engine dependency file:
GlobalProperties.HtmlEngineFullPath = "C:\app\bin\Select.Html.dep"
```

## Getting started

Import the `SelectPdf` namespace, create an `HtmlToPdf` converter, call `ConvertUrl` (or `ConvertHtmlString`) to get a `PdfDocument`, then `Save` it and `Close` it. Remember the 5-page cap on generated documents.

```csharp
using SelectPdf;

// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document to a file
doc.Save("Sample.pdf");

// close pdf document
doc.Close();
```

```vbnet
Imports SelectPdf

' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)

' save pdf document to a file
doc.Save("Sample.pdf")

' close pdf document
doc.Close()
```

To convert raw HTML use `converter.ConvertHtmlString(htmlString, baseUrl)`. In ASP.NET you can stream the result with the `Save(Response, openInline, fileName)` overload, e.g. `doc.Save(Response, false, "Sample.pdf")`.

## Rendering engines

The engine is selected through `HtmlToPdf.Options.RenderingEngine`, of type `RenderingEngine`. The enum values are `WebKit` (default), `WebKitRestricted`, `Blink`, and `Chromium`. `WebKit` is available on every supported .NET target and renders classic CSS plus ES5 JavaScript. `WebKitRestricted` is a reduced WebKit that runs in constrained environments such as Microsoft Azure Web Apps (also ES5 only). `Blink` is the Chrome/Chromium engine, available only on .NET Core 2.0+ and .NET Framework 4.6.1+, and renders modern HTML5/CSS3 with ES6 JavaScript; it requires the Blink companion package (or the downloaded Chromium folder). `Chromium` (introduced in v26.2) is based on the Chromium Embedded Framework (CEF), is available on every supported target, renders modern HTML5/CSS3 and ES2020+ JavaScript, and requires a Chromium.Windows companion package. Switching engines is a one-line change.

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

// save and close pdf document
doc.Save("Sample.pdf");
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

' save and close pdf document
doc.Save("Sample.pdf")
doc.Close()
```

Engine selection guidance: start with `WebKit`; for Azure Web Apps (Basic plan or above) use `WebKitRestricted` or `Chromium`; switch to `Blink` or `Chromium` when the input needs modern CSS3/HTML5 or modern JavaScript; prefer `Chromium` over `Blink` on legacy .NET Framework (2.0 – 4.5), on Azure Web Apps, or when you need to track a current Chromium release.

## Chromium and Blink specifics

Once the matching Chromium.Windows package is installed, select `RenderingEngine.Chromium` and use the standard conversion options as usual; the Chromium runtime folder is placed in the application output directory automatically at build time. You can override the engine binary locations through `HtmlToPdf.Options`: `CefEnginePath` points to the folder containing the Chromium (CEF) engine binary, and `BlinkEnginePath` points to the folder containing the Blink `chrome.exe`. The Blink engine supports high-concurrency conversions (since v26.3); its machine-wide simultaneous browser start-up limit defaults to half the processor count and can be changed with the `SELECTPDF_BLINK_MAX_CONCURRENT_LAUNCHES` environment variable (0 disables it).

```csharp
HtmlToPdf converter = new HtmlToPdf();

// select the Chromium rendering engine
converter.Options.RenderingEngine = RenderingEngine.Chromium;

// optional: override engine binary folders
converter.Options.CefEnginePath = @"C:\app\bin\Chromium";   // CEF engine folder
converter.Options.BlinkEnginePath = @"C:\app\bin\Chromium"; // Blink chrome.exe folder

// standard conversion options still apply
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

' optional: override engine binary folders
converter.Options.CefEnginePath = "C:\app\bin\Chromium"   ' CEF engine folder
converter.Options.BlinkEnginePath = "C:\app\bin\Chromium" ' Blink chrome.exe folder

' standard conversion options still apply
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

To deploy, ship `Select.HtmlToPdf.dll` together with `Select.Html.dep` in the same folder; alternatively set the path to `Select.Html.dep` through `GlobalProperties.HtmlEngineFullPath`. **Full trust** is required on the deployment machine because the library makes native API calls, so confirm full-trust support with shared hosting providers. When the Blink engine is used, deploy the full Chromium (Blink) folder; when the Chromium engine is used, deploy the Chromium runtime folder installed by the matching Chromium.Windows package — a standard publish picks it up automatically since MSBuild copies it to the output directory. The Chromium engine also runs on Microsoft Azure Web Apps (Basic plan or above) without additional configuration.

```csharp
using SelectPdf;

// Point the library at Select.Html.dep if it is not next to Select.HtmlToPdf.dll:
GlobalProperties.HtmlEngineFullPath = @"C:\app\bin\Select.Html.dep";
```

```vbnet
Imports SelectPdf

' Point the library at Select.Html.dep if it is not next to Select.HtmlToPdf.dll:
GlobalProperties.HtmlEngineFullPath = "C:\app\bin\Select.Html.dep"
```

See also: html-to-pdf.md, deployment-limits-troubleshooting.md
