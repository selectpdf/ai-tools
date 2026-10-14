# Installation, license key and deployment (Windows, Linux, macOS, Docker, cloud)

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## Installation

SelectPdf is installed exclusively through NuGet. There are no assemblies to download and no engine companion packages to pick from - the HTML rendering engine and the native PDF tool ship as per-platform NuGet packages that sit next to the managed library.

### Installation using NuGet Packages

A typical installation is two steps:

1. Install the **managed library** package (**SelectPdf.Universal**).
2. Install the **native engine** package that matches your deployment platform (runtime identifier), for example **SelectPdf.Universal.Native.win-x64**.

**1. Managed library package**

The full SelectPdf library. A single AnyCPU package that targets **.NET Standard 2.0**, **.NET 8.0** and **.NET 10.0** - usable from .NET Framework 4.6.1+, .NET Core 2.0+ / .NET Standard 2.0, and .NET 5 through .NET 10. The assembly is **SelectPdf.Universal.dll** and its public types live in the **SelectPdf.Universal** namespace (with **SelectPdf.Universal.Drawing** for the drawing primitives).

[https://www.nuget.org/packages/SelectPdf.Universal/](https://www.nuget.org/packages/SelectPdf.Universal/)

**2. Native engine packages** (install the one that matches your deployment runtime identifier)

The Chromium (CEF) HTML rendering engine and the native PDF tool used by the PDF-to-text and PDF-to-image converters are platform specific. Install exactly the Native package(s) matching the runtime identifier(s) you deploy to. The managed library is **AnyCPU**. Choose the Native package that matches the architecture of the process that converts - win-x64, linux-x64, linux-arm64 or osx-arm64 for a 64-bit process, win-x86 for a 32-bit Windows process: the Chromium engine then runs inside the application. With a package of another architecture conversions run in a separate engine process; see "The Chromium engine" (installation-and-deployment.md).

Native engines for 64-bit Windows.

[https://www.nuget.org/packages/SelectPdf.Universal.Native.win-x64/](https://www.nuget.org/packages/SelectPdf.Universal.Native.win-x64/)

Native engines for 32-bit Windows.

[https://www.nuget.org/packages/SelectPdf.Universal.Native.win-x86/](https://www.nuget.org/packages/SelectPdf.Universal.Native.win-x86/)

Native engines for 64-bit Linux (x64).

[https://www.nuget.org/packages/SelectPdf.Universal.Native.linux-x64/](https://www.nuget.org/packages/SelectPdf.Universal.Native.linux-x64/)

Native engines for 64-bit Linux (ARM64 / aarch64).

[https://www.nuget.org/packages/SelectPdf.Universal.Native.linux-arm64/](https://www.nuget.org/packages/SelectPdf.Universal.Native.linux-arm64/)

Native engines for macOS on Apple Silicon (ARM64). Requires macOS 13 (Ventura) or newer.

[https://www.nuget.org/packages/SelectPdf.Universal.Native.osx-arm64/](https://www.nuget.org/packages/SelectPdf.Universal.Native.osx-arm64/)

**3. Fonts package** (optional, for Linux)

Linux servers and container images often have no fonts. **SelectPdf.Universal.Fonts** adds Noto fonts for every script, so pages in any language render there; it copies nothing for Windows builds. See "Fonts and languages" (installation-and-deployment.md).

[https://www.nuget.org/packages/SelectPdf.Universal.Fonts/](https://www.nuget.org/packages/SelectPdf.Universal.Fonts/)

**4. One package per platform** (instead of steps 1 and 2)

For an application that runs on one platform, a single package installs both the managed library and the engine for that platform. These packages contain no code of their own. Install one of them, or the managed library and Native packages as described above - not both.

Windows x64: SelectPdf.Universal and the engine package for that platform.

[https://www.nuget.org/packages/SelectPdf.Universal.Windows/](https://www.nuget.org/packages/SelectPdf.Universal.Windows/)

Linux x64 and Docker: SelectPdf.Universal and the engine package for that platform.

[https://www.nuget.org/packages/SelectPdf.Universal.Linux/](https://www.nuget.org/packages/SelectPdf.Universal.Linux/)

Linux ARM64 and Docker: SelectPdf.Universal and the engine package for that platform.

[https://www.nuget.org/packages/SelectPdf.Universal.Linux.Arm64/](https://www.nuget.org/packages/SelectPdf.Universal.Linux.Arm64/)

macOS on Apple Silicon: SelectPdf.Universal and the engine package for that platform.

[https://www.nuget.org/packages/SelectPdf.Universal.MacOS/](https://www.nuget.org/packages/SelectPdf.Universal.MacOS/)

- All PDF features - HTML-to-PDF conversion, from-scratch document creation, graphic elements, fonts, encryption, bookmarks, viewer preferences, PDF/A, form filling, PDF compression, digital signatures, portfolios, page resize, PDF-to-text and PDF-to-image - are in-box in the **SelectPdf.Universal** package. The other packages are the optional **SelectPdf.Universal.Fonts**, fonts for every language on Linux (see "Fonts and languages" (installation-and-deployment.md)), and the one-package-per-platform installs of step 4.
- There is only one HTML rendering engine (Chromium / CEF), bundled in the Native package. There are no engine companion packages to choose. See "The Chromium engine" (installation-and-deployment.md).
- A project references one Native package. To publish one application for several platforms, see Publishing for Several Platforms.
- On macOS only Apple Silicon (ARM64) is supported by the Native package. On an Intel Mac the managed features still work, but HTML-to-PDF, PDF-to-text and PDF-to-image are unavailable because no **osx-x64** engine package is produced.

### Publishing for Several Platforms

A project references one Native package. Every Native package installs the engine into the same folder next to the application, so two of them in one build conflict.

To publish one application for several platforms, choose the Native package by runtime identifier and publish once per platform; each published copy then carries the engine for its platform. Add `Or '$(RuntimeIdentifier)' == ''` to the package for the computer you develop on, so that builds and debugging without a runtime identifier have an engine too:

```xml
<ItemGroup>
  <PackageReference Include="SelectPdf.Universal" Version="26.5.0" />
  <PackageReference Include="SelectPdf.Universal.Native.win-x64" Version="26.5.0"
    Condition="'$(RuntimeIdentifier)' == 'win-x64'
               Or '$(RuntimeIdentifier)' == ''" />
  <PackageReference Include="SelectPdf.Universal.Native.linux-x64" Version="26.5.0"
    Condition="'$(RuntimeIdentifier)' == 'linux-x64'" />
</ItemGroup>
```

```text
dotnet publish -c Release -r win-x64
dotnet publish -c Release -r linux-x64
```

### The Previous SelectPdf Library (Select.Pdf)

Before SelectPdf.Universal, the SelectPdf library shipped as **Select.Pdf**: a Windows-only library for .NET Framework and .NET, distributed as NuGet packages and as downloadable archives. SelectPdf.Universal is a separate product line with its own package ids, assembly names and namespace. NuGet does not treat it as an update of **Select.Pdf**, so an existing project keeps the previous library until its references are replaced. To move a project, remove the pieces listed below, install the packages described above, and follow "Migrating to SelectPdf.Universal" (migrating-from-classic.md) for the code changes.

**Previous NuGet packages and their replacements**

| Previous package | What it contained | Replace with |
|---|---|---|
| **Select.Pdf**, **Select.Pdf.x64** | The library for .NET Framework 2.0 and 4.0 (**Select.Pdf.dll**) with its WebKit HTML engine (**Select.Html.dep**) and its PDF tool (**Select.Tools.dep**), in a 32-bit and a 64-bit build. | **SelectPdf.Universal** plus one **SelectPdf.Universal.Native.<rid>** package. .NET Framework is supported from version 4.6.1, through the .NET Standard 2.0 build; .NET Framework 2.0 to 4.5 are not. |
| **Select.Pdf.NetCore**, **Select.Pdf.NetCore.x64** | The same library for .NET Framework 4.6.1 and later, .NET Core and .NET (.NET Standard 2.0), on Windows only. | **SelectPdf.Universal** plus one **SelectPdf.Universal.Native.<rid>** package. |
| **Select.Pdf.Chromium.Windows**, **Select.Pdf.Chromium.Windows.x64**, **Select.Pdf.NetCore.Chromium.Windows**, **Select.Pdf.NetCore.Chromium.Windows.x64** | The engine folder (**Chromium-CEF-<version>**) of the Chromium rendering engine. | Nothing separate: the Chromium engine is in the **SelectPdf.Universal.Native.<rid>** package. |
| **Select.Pdf.NetCore.Blink**, **Select.Pdf.NetCore.Blink.x64** | A Chrome build (**Chromium-<version>** folder) for the Blink rendering engine. | Nothing: SelectPdf.Universal has one rendering engine, Chromium. The Blink, WebKit and WebKit Restricted engines are not available. |
| **Select.Pdf.Extras**, **Select.Pdf.Extras.x64** | An add-on with an alternative forms manager and compressor (**Select.Pdf.Extras.dll**). | No add-on: form filling (`PdfFormManager`) and compression are part of **SelectPdf.Universal**. Code written against the add-on's own classes has to be rewritten. |

**Previous download archives**

The library was also offered as zip archives from the SelectPdf website: **SelectPdf-Assemblies-v<version>.zip** (the assemblies for each .NET Framework version, the documentation and the samples), **SelectPdf-Samples-v<version>.zip**, **SelectPdf-Chromium-Windows[-x64]-v<version>.zip** (the Chromium engine folder) and **SelectPdf-BlinkBinaries[-x64]-v<version>.zip** (the Blink engine folder). A project set up from an archive references the DLL directly and carries its engine files by hand. SelectPdf.Universal has no archive distribution: it is installed through NuGet only.

**What to remove from a project that used the previous library**

- The package references listed in the table above, and **Newtonsoft.Json** if it was added only for SelectPdf.
- Direct references to **Select.Pdf.dll**, **Select.HtmlToPdf.dll** or **Select.Pdf.Extras.dll**, usually with a hint path into the **Assemblies** folder of an archive.
- The engine files deployed next to the application, whether they were added to the project as content or copied by hand: **Select.Html.dep**, **Select.Tools.dep**, the Chromium engine folder (**Chromium-CEF-<version>**) and the Blink engine folder (**Chromium-<version>**). Clean the output folders as well, where copies can remain after the references are gone.
- Build steps that copied the engine files: projects that use **packages.config** often carry an **AfterBuild** target (named **CopyChromiumFiles** in the SelectPdf samples) that copies the Chromium folder into the output. The new native packages deploy their files themselves.
- On machines where **Select.Pdf** or **Select.HtmlToPdf** was installed in the Global Assembly Cache, remove it from the GAC once no application uses it.

The new assemblies are named **SelectPdf.Universal.dll** and **SelectPdf.HtmlToPdf.Universal.dll**, and the engine files are the **Chromium-CEF-154.0.28** folder and **SelectPdf.Tools.dep** (**SelectPdf.Tools** on Linux and macOS). SelectPdf.Universal loads nothing from the previous library, so leftover files do not affect it, but they add to the size of every deployment.

**License keys**

SelectPdf.Universal reads its license key from `GlobalProperties.LicenseKey`, in the same format as the previous library. A key issued for version 26 or later is accepted; a key for an earlier major version is refused as not valid for this version, as the previous library refuses it too. See "License key" (installation-and-deployment.md).

## License key

### Acquiring a license

SelectPdf Library for .NET is provided as a free trial product that can be tested without any limitations for 30 days.

The free trial is fully functional, but without a license key, an watermark will be displayed in each page of the pdf documents generated with the library. A license key is need to unlock the product and remove the demo watermark.

The same license key works with all SelectPdf targets (.NET Standard 2.0, .NET 8.0, .NET 10.0 - usable from .NET Framework 4.6.1+, .NET Core 2.0+ and .NET 5 through .NET 10).

A license key can be purchased from the product website: [https://selectpdf.com](https://selectpdf.com).

Take a look at the End User License Agreement for licensing details.

### Applying the license key

Once the license is purchased, a license key is delivered by email.

If you previously used the Community Edition for tests, switch the package reference to the full SelectPdf commercial library (**SelectPdf.Universal**). Because both editions use the same `SelectPdf.Universal` namespace, no code change is required.

If you used the free trial of the commercial library for tests, there is no need to download another version of the product, as the free trial is a full version. To unlock the product, the license key needs to be set in code, using the `GlobalProperties.LicenseKey` property of the `GlobalProperties` class:

```csharp
	// sample license key setting
    SelectPdf.Universal.GlobalProperties.LicenseKey = "your-license-key";
```

```vbnet
	' sample license key setting
    SelectPdf.Universal.GlobalProperties.LicenseKey = "your-license-key"
```

Do not forget to replace **your-license-key** with the actual license key received by email when the order is processed.

> **Important:** If the `GlobalProperties.LicenseKey` property cannot be found in `GlobalProperties` class, make sure that you use the full SelectPdf Commercial Library (the **SelectPdf.Universal** package, **SelectPdf.Universal.dll**) and not SelectPdf Html To Pdf Converter Community Edition (the **SelectPdf.HtmlToPdf.Universal** package, **SelectPdf.HtmlToPdf.Universal.dll**).

## The Chromium engine

SelectPdf.Universal renders HTML with the **Chromium** rendering engine, based on the **Chromium Embedded Framework (CEF)**. Chromium is the only engine, and it runs in one of two places: on the machine the application runs on (`RenderingEngine.Chromium`, the default), or in a remote SelectPdf engine that the application reaches over HTTP (`RenderingEngine.ChromiumRemote`, see "The remote engine" (installation-and-deployment.md)). A typical conversion needs no engine selection. Because the engine tracks a current Chromium release, it renders modern HTML5/CSS3 and modern JavaScript (ES2020+) correctly.

For an engine on the application's own machine, the engine ships in the platform-specific **SelectPdf.Universal.Native.<rid>** NuGet package (for example **SelectPdf.Universal.Native.win-x64** or **SelectPdf.Universal.Native.linux-x64**) that is installed alongside the managed **SelectPdf.Universal** package - see "Installation" (installation-and-deployment.md). The engine runs headless / off-screen, so no display server is required. It runs on Windows (x64, x86), Linux (x64, arm64) and macOS (Apple Silicon), and on Microsoft Azure Web Apps (Basic plan or above) without additional configuration - see "Microsoft Azure" (installation-and-deployment.md).

### NuGet Packages

Install the managed **SelectPdf.Universal** package and the **SelectPdf.Universal.Native.<rid>** package matching your deployment runtime identifier. The Native package deploys the Chromium runtime (engine folder **Chromium-CEF-154.0.28**) into the application output folder automatically at build time.

Chromium engine for 64-bit Windows.

Chromium engine for 32-bit Windows.

Chromium engine for 64-bit Linux (x64).

Chromium engine for 64-bit Linux (ARM64 / aarch64).

Chromium engine for macOS on Apple Silicon (ARM64), macOS 13 (Ventura) or newer.

> **Note:** Choose the native package that matches the architecture of the application's process: the engine then runs inside the application (see Engine Hosting below). With a package of another architecture, for example a 32-bit process with the win-x64 package, conversions still work, in a separate engine process. The Linux packages carry the Chromium system libraries the engine needs, so a minimal server or container image needs no additional packages; see "Deployment" (installation-and-deployment.md).

### Selecting the Chromium Engine

Chromium running on this machine is the default, so this step is optional. To set it explicitly, assign `HtmlToPdfOptions.RenderingEngine` to `RenderingEngine.Chromium`. The other value, `RenderingEngine.ChromiumRemote`, sends the conversion to a remote SelectPdf engine instead - see "The remote engine" (installation-and-deployment.md). When the property is not set, the converter uses `GlobalProperties.RenderingEngine`:

```csharp
HtmlToPdf converter = new HtmlToPdf();

// Chromium is the default; setting it explicitly is optional
converter.Options.RenderingEngine = RenderingEngine.Chromium;

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

' Chromium is the default; setting it explicitly is optional
converter.Options.RenderingEngine = RenderingEngine.Chromium

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

The path to the engine folder can be overridden with `HtmlToPdfOptions.ChromiumEnginePath` when the runtime is deployed to a non-default location. Every type that drives the engine carries that property, and `ChromiumEngineTemporaryFilesPath`, `LFN` and `RenderingEngine` with it: `HtmlToPdfOptions`, `PdfHtmlElement`, `PdfHtmlSection`, `HtmlToImage` and `HtmlToImageElement` - so an application that stages the engine elsewhere can say so wherever it renders html.

### HTML to Image with the Chromium Engine

The same engine powers `HtmlToImage`, which renders HTML to a PNG image returned as a `byte[]`:

```csharp
HtmlToImage converter = new HtmlToImage();

byte[] png = converter.ConvertUrl("https://selectpdf.com");
System.IO.File.WriteAllBytes("ChromiumSample.png", png);
```

```vbnet
Dim converter As New HtmlToImage()

Dim png As Byte() = converter.ConvertUrl("https://selectpdf.com")
System.IO.File.WriteAllBytes("ChromiumSample.png", png)
```

### Converting Authenticated Pages (Login Options)

The engine can convert pages behind an HTML login form by populating `HtmlToPdfOptions.LoginOptions` on the converter options before conversion. `LoginOptions` takes the login page URL, the CSS selectors of the username, password and submit button fields, the credentials to type in, and optional delays before typing and after submitting.

```csharp
HtmlToPdf converter = new HtmlToPdf();

converter.Options.LoginOptions = new LoginOptions
{
    LoginPage = "https://example.com/login",
    UsernameFieldSelector = "#username",
    PasswordFieldSelector = "#password",
    SubmitButtonSelector = "#submit",
    Username = "user@example.com",
    Password = "secret",
    DelayBefore = 100,
    DelayAfter = 500
};

PdfDocument doc = converter.ConvertUrl("https://example.com/protected");

try
{
    doc.Save("ProtectedPage.pdf");
}
finally
{
    doc.Close();
}
```

```vbnet
Dim converter As New HtmlToPdf()

converter.Options.LoginOptions = New LoginOptions() With {
    .LoginPage = "https://example.com/login",
    .UsernameFieldSelector = "#username",
    .PasswordFieldSelector = "#password",
    .SubmitButtonSelector = "#submit",
    .Username = "user@example.com",
    .Password = "secret",
    .DelayBefore = 100,
    .DelayAfter = 500
}

Dim doc As PdfDocument = converter.ConvertUrl("https://example.com/protected")

Try
    doc.Save("ProtectedPage.pdf")
Finally
    doc.Close()
End Try
```

### Engine Hosting: In-Process and Out-of-Process

The engine can run in two ways, selected for the whole application by `GlobalProperties.ChromiumEngineHostMode`:

The engine is loaded into the application once and kept ready between conversions. This is the default when the in-process host library (**SelectPdf.Cef.Host.dll**, **.so** or **.dylib**) is beside the engine, as it is in every current native package. It removes the time spent starting and closing a browser for every conversion, so short pages convert several times faster.

Every conversion starts a short-lived engine process of its own. Nothing is shared between conversions, and a crash or a hang inside the browser cannot affect the application. This is the default when the host library is not beside the engine.

Set the mode during start-up, before the first conversion: the value in force at the first conversion is used for the life of the process. The environment variable `SELECTPDF_CHROMIUM_ENGINE_HOST` (`inprocess` or `outofprocess`) changes the default without a code change; an explicit assignment overrides it.

```csharp
// at application start-up, before the first conversion:
// run every conversion in its own engine process
GlobalProperties.ChromiumEngineHostMode = ChromiumEngineHostMode.OutOfProcess;
```

```vbnet
' at application start-up, before the first conversion:
' run every conversion in its own engine process
GlobalProperties.ChromiumEngineHostMode = ChromiumEngineHostMode.OutOfProcess
```

What running the engine inside the application means:

- Up to `HtmlToPdfOptions.MaximumConcurrentConversions` conversions (8 by default) run at the same time, each in a browser of its own with its own cookies and storage. Further conversions wait and are served in the order they arrived. The same limit applies out of process.
- The HTTP cache is kept from one conversion to the next by default, which makes pages that share resources faster to convert. Set `HtmlToPdfOptions.HttpCache` to `HttpCacheMode.Clear` when conversions run on behalf of different users whose responses must stay separate (see "Security recommendations" (troubleshooting-and-security.md)). Cookies and site storage are cleared after every conversion in every mode.
- Settings that apply to the whole browser - `HtmlToPdfOptions.WebGlEnabled`, the proxy, insecure content and local file access - are fixed by the first conversion.
- The engine runs inside the application only when the native package matches the architecture of the application's process: win-x64, linux-x64, linux-arm64 or osx-arm64 for a 64-bit process, win-x86 for a 32-bit Windows process (a Windows App Service worker is 32-bit by default). Otherwise conversions run out of process automatically, and assigning `ChromiumEngineHostMode.InProcess` throws.
- The browser's threads run in the application's process, and its renderer and helper processes stay alive until the application exits.

One conversion can run in its own engine process whatever the mode is, with `HtmlToPdfOptions.ForceOutOfProcess`: use it for a page you do not trust, one likely to crash or hang the browser, or one that needs a browser-wide setting other than the one the first conversion fixed. It costs the start of an engine process.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// run this conversion in a separate engine process
converter.Options.ForceOutOfProcess = true;

PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");
doc.Save("Isolated.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' run this conversion in a separate engine process
converter.Options.ForceOutOfProcess = True

Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")
doc.Save("Isolated.pdf")
doc.Close()
```

### WebGL and conversion speed

To render WebGL content the engine has to initialize a software graphics stack for every conversion. Most documents contain no WebGL at all, so `HtmlToPdfOptions.WebGlEnabled` is False by default and that initialization is skipped. Set it to True for pages that draw with WebGL (map or 3D chart libraries, for example).

The time WebGL costs is fixed per conversion rather than proportional, so it is most visible on small documents: on a typical Windows host a short html page converts in roughly a quarter of the time with WebGL off. A large document saves the same amount of time, which is proportionally less of its total.

This applies to **Windows**. On **Linux** that initialization is not a measurable part of a conversion, so turning WebGL on there costs nothing noticeable.

With WebGL off, a page whose content *is* WebGL does not draw - the browser reports no WebGL context. Ordinary 2D canvas content is **not** affected and renders identically either way. When the engine runs inside the application, the value of the first conversion applies to the whole process; use `HtmlToPdfOptions.ForceOutOfProcess` for a conversion that needs the other value.

The property is available on `HtmlToPdfOptions` and on every other type that renders html: `PdfHtmlElement`, `PdfHtmlSection`, `HtmlToImage` and `HtmlToImageElement`.

### Feature Notes

The Chromium engine supports the full SelectPdf HTML-to-PDF feature set - including automatic bookmark generation, POST data, web elements location retrieval, table of contents generation, and partial web page rendering (converting only a CSS-selected region of the input document via `HtmlToPdfOptions.VisibleWebElementSelector`).

## The remote engine

The conversion engines normally run on the machine the application runs on, from the **SelectPdf.Universal.Native.<rid>** package. With `RenderingEngine.ChromiumRemote` the application sends that work over HTTP to the **SelectPdf engine** instead - a service that ships as the container image `ghcr.io/selectpdf/engine` and runs on another machine, as another container, or beside the application in the same pod.

Every native operation goes to the engine: HTML to PDF and HTML to image (the Chromium engine), and PDF to text and PDF to image (the PDF tool). Everything else stays in the application and works exactly as before: building the document, headers and footers, bookmarks, PDF/A and PDF/UA, security and signatures, Word to PDF, redaction, forms - and the license check. The engine itself needs no license.

Use the remote engine to:

- **Run on Alpine Linux** and other musl-based systems, where the native engines cannot run. This is the supported way to use the library there.
- **Keep the application small**: it references only the managed **SelectPdf.Universal** package - no native package and no fonts package.
- **Isolate rendering** from the application, scale it separately, or share one engine between several applications.

### Quick start

Start the engine with an access token:

```text
docker run -d --name selectpdf-engine -p 8080:8080 \
  --read-only --tmpfs /tmp \
  -e SELECTPDF_ENGINE_TOKEN="$(openssl rand -base64 32)" \
  ghcr.io/selectpdf/engine:26.5
```

Then point the application at it, once at startup. The conversion code does not change:

```csharp
GlobalProperties.RenderingEngine = RenderingEngine.ChromiumRemote;
GlobalProperties.EngineEndpoint = "http://localhost:8080";
GlobalProperties.EngineAccessToken = "the token the engine was started with";

HtmlToPdf converter = new HtmlToPdf();
PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");
doc.Save("test.pdf");
doc.Close();
```

```vbnet
GlobalProperties.RenderingEngine = RenderingEngine.ChromiumRemote
GlobalProperties.EngineEndpoint = "http://localhost:8080"
GlobalProperties.EngineAccessToken = "the token the engine was started with"

Dim converter As New HtmlToPdf()
Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")
doc.Save("test.pdf")
doc.Close()
```

The same settings can come from environment variables instead, with no code at all - which is how a container deployment usually does it:

```text
SELECTPDF_RENDERING_ENGINE=ChromiumRemote
SELECTPDF_ENGINE_ENDPOINT=http://selectpdf-engine:8080
SELECTPDF_ENGINE_TOKEN=the token the engine was started with
```

The samples include a complete Docker Compose example, **docker/remote-engine**: an application on `mcr.microsoft.com/dotnet/aspnet:10.0-alpine` and the engine as two services.

### Application settings

| Property | Environment variable | Meaning |
|---|---|---|
| `GlobalProperties.RenderingEngine` | `SELECTPDF_RENDERING_ENGINE` | `Chromium` (the default: the engines run on this machine) or `ChromiumRemote`. Every converter follows it unless its own **RenderingEngine** property is set, and the PDF tools always follow it. |
| `GlobalProperties.EngineEndpoint` | `SELECTPDF_ENGINE_ENDPOINT` | The engine's url. Default `http://selectpdf-engine:8080`: an engine service named `selectpdf-engine` on the same Docker network or cluster. |
| `GlobalProperties.EngineAccessToken` | `SELECTPDF_ENGINE_TOKEN` | One of the tokens the engine was started with, or none when the engine requires none. |
| `GlobalProperties.EngineTimeout` | `SELECTPDF_ENGINE_TIMEOUT` (seconds) | How long one request may take. By default it follows the conversion's own navigation and print timeouts, plus time to wait for a free slot on a busy engine. |

A single converter can use the other engine than the global setting through its own `HtmlToPdfOptions.RenderingEngine` (and the **RenderingEngine** property of **HtmlToImage**, **PdfHtmlElement**, **PdfHtmlSection** and **HtmlToImageElement**).

### Running the engine

The image is `ghcr.io/selectpdf/engine` (mirrored on Docker Hub as `selectpdf/engine`), for **x64** and **arm64**. Use the tag of the library's version - `26.5.0`, or `26.5` for its latest build; an engine that does not speak the library's protocol is reported as such. The engine listens on port 8080, runs as a non-root user, works with a read-only file system and a writable `/tmp`, and needs no extra capabilities and no `--shm-size`.

**Health:** `GET /health` answers 200 once the engine has done its first conversion (the image's own `HEALTHCHECK` uses it), and 503 before that or when it cannot convert. `/healthz` answers the same, for probes written with that name. `GET /v1/info` (with the token) reports the engine's version, its Chromium version and its limits.

| Engine environment variable | Meaning |
|---|---|
| `SELECTPDF_ENGINE_TOKEN` | The access tokens the engine accepts, separated by commas - several at once let a token be rotated without downtime. Each must be at least 32 characters, or the engine does not start; generate one with `openssl rand -base64 32`. Without a token anyone who can reach the port can use the engine (it logs a warning). |
| `SELECTPDF_ENGINE_TOKEN_FILE` | A file holding the tokens, one per line - a Docker or Kubernetes secret. Instead of `SELECTPDF_ENGINE_TOKEN`. |
| `SELECTPDF_MAX_CONCURRENCY` | HTML conversions run at once. Default: the number of CPUs the container has. |
| `SELECTPDF_MAX_TOOL_CONCURRENCY` | PDF to text / image operations run at once. Default: the number of CPUs. |
| `SELECTPDF_QUEUE_TIMEOUT` | Seconds a request waits for a free slot (default 30). After that the engine answers 503 with `Retry-After: 1` and closes the connection, so a retry can reach another replica; the library retries such an answer up to three times. |
| `SELECTPDF_MAX_BODY_MB` | The largest request accepted, in MB (default 100). |
| `SELECTPDF_ENGINE_HTTP_CACHE` | The most of the HTTP cache a conversion may keep: `clear`, `thirdParty` or `all` (the default - each conversion keeps what it asks for through `HtmlToPdfOptions.HttpCache`). Set `clear` on an engine shared by different callers. |
| `SELECTPDF_ENGINE_PORT` | The port to listen on (default 8080) - change it when the engine runs beside an application that uses 8080 in the same pod. |
| `SELECTPDF_TEMP_DIR` | Where conversions work (default `/tmp/selectpdf-engine`). Each request has a folder of its own, deleted when it ends. |

**Docker Compose:** name the engine service `selectpdf-engine` and the application needs no endpoint setting at all:

```text
services:
  selectpdf-engine:
    image: ghcr.io/selectpdf/engine:26.5
    read_only: true
    tmpfs:
      - /tmp
    environment:
      SELECTPDF_ENGINE_TOKEN: ${SELECTPDF_ENGINE_TOKEN}
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      SELECTPDF_RENDERING_ENGINE: ChromiumRemote
      SELECTPDF_ENGINE_TOKEN: ${SELECTPDF_ENGINE_TOKEN}
    depends_on:
      selectpdf-engine:
        condition: service_healthy
```

**Kubernetes, Azure, AWS and Google Cloud:** see Running the engine in the cloud below.

### Running the engine in the cloud

Each route below keeps the engine off the internet and was tested end to end on its platform with the image and an application on `aspnet:10.0-alpine`. Pick the one where the application already runs. The token rule decides the shape of each: the library sends the token over plain HTTP only to a local endpoint (a name without a dot, `localhost`, a `.svc.cluster.local` name or a private address), and over HTTPS anywhere else.

The samples download has a step-by-step sample for Azure Container Apps, Amazon ECS, Google Cloud Run and Kubernetes in `cloud/remote-engine`, each deploying the application of `docker/remote-engine` beside the engine.

#### Kubernetes (Helm)

The chart runs the engine as a Deployment behind a Service, non-root with a read-only root file system, with `/health` as its startup, readiness and liveness probe and the token in a Secret. Install it as `selectpdf-engine` and an application in the same namespace needs no endpoint setting:

```text
helm install selectpdf-engine \
  oci://ghcr.io/selectpdf/charts/selectpdf-engine --version 26.5.0 \
  --namespace pdf --create-namespace \
  --set auth.token="$(openssl rand -base64 32)"
```

From another namespace use `http://selectpdf-engine.<namespace>.svc.cluster.local:8080`. Other values: `replicaCount` (2), `auth.existingSecret` for a Secret you manage, `engine.httpCache`, `engine.maxConcurrency` and `resources`. Several tokens are separated by commas, which Helm's `--set` splits on: escape them (`--set auth.token='a\,b'`) or put the value in a values file.

#### Azure Container Apps

Run the engine as an app with internal ingress named `selectpdf-engine`: apps in the same environment reach it as `http://selectpdf-engine`, a local name, and nothing outside the environment can.

```text
az containerapp create -n selectpdf-engine -g my-group \
  --environment my-environment \
  --image ghcr.io/selectpdf/engine:26.5 \
  --ingress internal --target-port 8080 --transport http \
  --cpu 2 --memory 4Gi --min-replicas 1 --max-replicas 3 \
  --secrets "engine-token=$TOKEN" \
  --env-vars SELECTPDF_ENGINE_TOKEN=secretref:engine-token
```

and in the application's app:

```text
--secrets "engine-token=$TOKEN" \
--env-vars SELECTPDF_RENDERING_ENGINE=ChromiumRemote \
  SELECTPDF_ENGINE_ENDPOINT=http://selectpdf-engine \
  SELECTPDF_ENGINE_TOKEN=secretref:engine-token
```

Keep `--min-replicas 1`: an engine scaled to zero starts Chromium on the first request after it.

#### Azure Container Instances

Run the engine as a second container in the application's container group. The containers share `localhost`, so the engine listens on another port and only the application's port is exposed:

```text
containers:
  - name: selectpdf-engine
    properties:
      image: ghcr.io/selectpdf/engine:26.5
      resources:
        requests: { cpu: 2, memoryInGB: 4 }
      environmentVariables:
        - name: SELECTPDF_ENGINE_PORT
          value: '8081'
        - name: SELECTPDF_ENGINE_TOKEN
          secureValue: '<token>'
  - name: app
    properties:
      image: myregistry.azurecr.io/my-app:1.0
      ports:
        - port: 8080
      resources:
        requests: { cpu: 1, memoryInGB: 1.5 }
      environmentVariables:
        - name: SELECTPDF_RENDERING_ENGINE
          value: ChromiumRemote
        - name: SELECTPDF_ENGINE_ENDPOINT
          value: http://localhost:8081
        - name: SELECTPDF_ENGINE_TOKEN
          secureValue: '<token>'
```

Deploy the group with `az container create --file group.yaml`, with `ipAddress` publishing port 8080 only.

#### Amazon ECS on Fargate

**As its own service, with Service Connect.** Run the engine as a service that publishes port 8080 under the client alias `selectpdf-engine` - the library's default endpoint, so the application needs no endpoint setting - and turn Service Connect on in the application's service too. The engine's task definition:

```text
"containerDefinitions": [{
  "name": "engine",
  "image": "ghcr.io/selectpdf/engine:26.5",
  "readonlyRootFilesystem": true,
  "portMappings": [{ "name": "engine", "containerPort": 8080,
                     "protocol": "tcp", "appProtocol": "http" }],
  "secrets": [{ "name": "SELECTPDF_ENGINE_TOKEN",
                "valueFrom": "<Secrets Manager secret ARN>" }],
  "mountPoints": [{ "sourceVolume": "tmp", "containerPath": "/tmp" }],
  "healthCheck": {
    "command": ["CMD", "dotnet", "/app/SelectPdf.Engine.Host.dll",
                "--healthcheck"],
    "interval": 30, "timeout": 10, "retries": 3, "startPeriod": 60 }
}],
"volumes": [{ "name": "tmp" }],
"cpu": "2048", "memory": "4096"
```

and its service:

```text
aws ecs create-service --cluster my-cluster \
  --service-name selectpdf-engine --task-definition selectpdf-engine \
  --launch-type FARGATE --desired-count 2 \
  --network-configuration "awsvpcConfiguration={subnets=[...],
    securityGroups=[engine-sg]}" \
  --service-connect-configuration '{"enabled":true,
    "namespace":"my-namespace","services":[{"portName":"engine",
    "discoveryName":"selectpdf-engine","clientAliases":[
    {"port":8080,"dnsName":"selectpdf-engine"}]}]}'
```

Give the engine's security group port 8080 from the application's security group only. The image declares `/tmp` as a volume, which is what lets the non-root engine write to the task volume with a read-only root file system. Reading the token from Secrets Manager needs `secretsmanager:GetSecretValue` on the task execution role.

**In the application's task.** Add the engine as a second container with `SELECTPDF_ENGINE_PORT` set to `8081`, give the application `SELECTPDF_ENGINE_ENDPOINT=http://localhost:8081`, and make it start after the engine with `"dependsOn": [{"containerName": "engine", "condition": "HEALTHY"}]`. Size the task for both (2 vCPU and 4 GB or more).

#### Google Cloud Run

Run the engine as its own service and give the application its HTTPS URL:

```text
gcloud run deploy selectpdf-engine \
  --image ghcr.io/selectpdf/engine:26.5 \
  --region europe-north1 --port 8080 --cpu 2 --memory 4Gi \
  --concurrency 8 --timeout 600 --execution-environment gen2 \
  --no-invoker-iam-check \
  --set-secrets SELECTPDF_ENGINE_TOKEN=engine-token:latest
```

```text
SELECTPDF_RENDERING_ENGINE=ChromiumRemote
SELECTPDF_ENGINE_ENDPOINT=https://selectpdf-engine-xxxxxxxxxx-lz.a.run.app
SELECTPDF_ENGINE_TOKEN=<the same token>
```

`--no-invoker-iam-check` is required: without it Cloud Run checks the `Authorization` header of a request coming from another Cloud Run service as a Google identity token, and refuses the engine's token before it reaches the engine. The engine's own token check still applies. Check the engine's health from outside at `/health`: Cloud Run serves no external path ending in `z`, so the older `/healthz` answers 404 there. Cloud Run pulls the public image from ghcr.io directly; Google recommends Artifact Registry, which can mirror it through a remote repository.

### Security

- **Use a token** and keep the engine off the public internet - on the Docker network, the cluster network or a private subnet of the application.
- **The token is never sent in clear text to another network.** With a token set, the library requires an `https` endpoint unless the endpoint is a local one: a service name without a dot, `localhost`, a loopback or a private address. The engine itself serves plain HTTP; for an engine on another machine, put TLS in front of it (an ingress, a load balancer or a reverse proxy).
- **A page cannot read the engine's files**: local file access is always off in the engine, whatever a conversion asks for.
- **The engine fetches what the pages reference**, from its own network. When it converts html you do not control, limit where the engine's network can reach (internal services, cloud metadata addresses).
- **A shared engine**: set `SELECTPDF_ENGINE_HTTP_CACHE=clear`, so no conversion is served a response another caller's conversion fetched. Cookies and storage are cleared after every conversion in any case.

### What is different from a local engine

- **Local files:** the engine cannot see the application's files, so a `file://` url or base url is refused before anything is sent. Convert the html as a string, or serve the files over http. Local files an html string references - images, stylesheets, fonts and media in `src`, `href`, `srcset` or a CSS `url()` - are sent with it as `data:` urls, so they are drawn as a local engine draws them (up to 48 MB in one conversion; not when local file access is denied, as locally). Relative urls inside an inlined stylesheet do not resolve.
- **localhost** in a url means the engine's own machine, not the application's - except when the engine runs in the application's pod or on its host network. A conversion of a `localhost` or loopback url that fails says so in its message; use an address the engine can reach, such as the application's service name.
- **Fonts:** the engine has fonts for every language (the Noto family) and renders with them, as a Linux application with the **SelectPdf.Universal.Fonts** package does. Fonts in the application's own **fonts** folder are sent to the engine with each conversion. See "Fonts and languages" (installation-and-deployment.md).
- **Per-conversion settings that a local in-process engine fixes for the whole process** - `HtmlToPdfOptions.WebGlEnabled`, a proxy, insecure content - are honoured per conversion: the engine runs such a conversion in a process of its own. A proxy must be reachable from the engine.
- **Errors** are the same exception types as locally (`HtmlToPdfException`, `PdfToTextException`, ...). When the engine itself could not be reached or refused the request, the message says so and names the request id the engine logs its runs under.
- **Logging:** with a log file set (**LFN**), the engine's own log of the conversion is appended to it.
- **Cancellation:** cancelling an awaited conversion (**ConvertUrlAsync** and the other asynchronous methods) cancels it in the engine.

### Sizing

One engine converts several pages at once - as many as it has CPUs by default - and queues the rest. Measured with a light document of one or two pages, one engine container limited to N CPUs:

| CPUs | One conversion | Conversions per second, fully loaded |
|---|---|---|
| 1 | about 100 ms | about 7 |
| 2 | about 60 ms | about 10 |
| 4 | about 50 ms | about 18 |
| 8 | about 50 ms | about 24 |

Real pages take longer, mostly in fetching them. Throughput grows little beyond 4 to 8 CPUs per engine; for more, run more engine replicas behind one service. Memory stayed under 500 MB with eight light conversions at once; heavy pages (news sites, long reports) need more - give each engine 2 GB or more.

## Fonts and languages

HTML is rendered by a Chromium engine, which takes the fonts a page asks for from the computer it runs on, and text the library draws itself (headers, footers, text elements) uses fonts from the same place. On Windows and macOS that just works: both come with fonts for every major script. A Linux server, and almost every Linux container image, comes with few fonts or none.

SelectPdf brings three things to deal with that, from the smallest to the most complete: fonts built into the library, the **SelectPdf.Universal.Fonts** package, and your application's own `fonts` folder.

### Fonts built into the library

The library carries the Liberation fonts (Sans, Serif and Mono, regular, bold and italic), which have the same measurements as Arial, Times New Roman and Courier New. When the computer has no font for one of those families, or for the generic families sans-serif, serif and monospace, these are used, so a page in a Western European language prints correctly on any Linux host, even one with no fonts at all.

They cover Latin, Greek and Cyrillic text only. Without other fonts, every other script prints as empty boxes.

### The SelectPdf.Universal.Fonts package

The **SelectPdf.Universal.Fonts** package contains Google's Noto fonts (SIL Open Font License 1.1): Sans, Serif and Mono for every script Noto covers - Latin, Greek, Cyrillic, Arabic, Hebrew, the Indic and Southeast Asian scripts, Georgian, Armenian, Ethiopic, Tibetan and more - plus Noto Sans CJK for Chinese, Japanese and Korean (regular and bold) and Noto Color Emoji. About 270 files and 92 MB.

Reference it next to the other two packages:

```xml
<ItemGroup>
  <PackageReference Include="SelectPdf.Universal" Version="26.5.0" />
  <PackageReference Include="SelectPdf.Universal.Native.linux-x64"
                    Version="26.5.0" />
  <PackageReference Include="SelectPdf.Universal.Fonts" Version="26.5.0" />
</ItemGroup>
```

On build and publish the fonts are copied to `fonts/noto` next to the application, where the Chromium engine and the library find them; nothing has to be installed on the server and no setting is needed. They are used only for text the computer has no font for: fonts installed on the computer still win.

- Use it for every deployment to Linux - containers, AWS Lambda, Azure App Service for Linux, Google Cloud Run - unless you know the target has the fonts your pages need.
- The fonts are not copied when the application is built for a Windows runtime identifier (`win-x64`, `win-x86`), because Windows has these scripts already. To leave them out of any other build, set the `SelectPdfFonts` property to `false`.

### Your own fonts

When your HTML asks for a font by name - a corporate font, for example - that the server does not have, put the font files (.ttf, .otf, .ttc) in a `fonts` folder of your project and have them copied to the output:

```xml
<ItemGroup>
  <None Include="fonts\**" CopyToOutputDirectory="PreserveNewest"
        CopyToPublishDirectory="PreserveNewest" />
</ItemGroup>
```

The folder ends up next to the application, and SelectPdf uses its fonts on every platform, with or without the fonts package (whose files go to the `noto` subfolder of the same folder):

- **Linux** - the Chromium engine is pointed at the folder, so the fonts behave exactly like installed fonts.
- **Windows and macOS** - Chromium looks only at the fonts installed on the computer there, so SelectPdf offers each font of the folder (outside `noto`) to the page as a web font. A page asking for the family by name gets it. Fonts over 16 MB, and fonts past 32 MB in total, are not offered.
- **Text the library draws** - `PdfFont.CreateFromSystemFont(string, float)` and `PdfSystemFonts` find the families of the folder too, after the fonts installed on the computer.

With the font in place, nothing else changes in the code:

```csharp
// fonts/LobsterTwo-Regular.otf is copied next to the application
HtmlToPdf converter = new HtmlToPdf();

// text drawn by the library finds the font too
converter.Options.DisplayHeader = true;
converter.Header.Height = 40;
PdfFont font = PdfFont.CreateFromSystemFont("Lobster Two", 14);
converter.Header.Add(new PdfTextSection(0, 10, "Monthly report", font));

// and so does the page
PdfDocument doc = converter.ConvertHtmlString(
    "<p style=\"font-family: 'Lobster Two'\">Hello</p>");
doc.Save("report.pdf");
doc.Close();
```

```vbnet
' fonts/LobsterTwo-Regular.otf is copied next to the application
Dim converter As New HtmlToPdf()

' text drawn by the library finds the font too
converter.Options.DisplayHeader = True
converter.Header.Height = 40
Dim font As PdfFont = PdfFont.CreateFromSystemFont("Lobster Two", 14)
converter.Header.Add(New PdfTextSection(0, 10, "Monthly report", font))

' and so does the page
Dim doc As PdfDocument = converter.ConvertHtmlString(
    "<p style=""font-family: 'Lobster Two'"">Hello</p>")
doc.Save("report.pdf")
doc.Close()
```

> **Note:** Check that the license of each font allows you to distribute it with your application.

### Installing fonts in a container image instead

Fonts installed in a Linux image the usual way (`apt-get install fonts-noto-core fonts-noto-cjk` on Debian and Ubuntu, `dnf install google-noto-cjk-fonts` on Amazon Linux) are used too, even on minimal images that do not include fontconfig's own configuration. The fonts package does the same without a package manager, which also covers platforms where the image cannot be changed, such as Azure App Service for Linux.

## Deployment

SelectPdf runs on **Windows (x64, x86)**, **Linux (x64, arm64)** and **macOS (Apple Silicon)**. A deployment consists of the managed library plus the native engines for the target platform:

- **SelectPdf.Universal.dll** - the managed library (from the **SelectPdf.Universal** package).
- The **Chromium (CEF) engine folder** (**Chromium-CEF-154.0.28**) used by the HTML-to-PDF / HTML-to-image converters.
- The native PDF tool (**SelectPdf.Tools.dep** on Windows, **SelectPdf.Tools** on Linux and macOS) used by the PDF-to-text and PDF-to-image converters.

These native engines are supplied by the matching **SelectPdf.Universal.Native.<rid>** package (**win-x64**, **win-x86**, **linux-x64**, **linux-arm64** or **osx-arm64**). When the project references that package, MSBuild places the engine files in the application output directory automatically at build time, so a standard publish operation picks them up. See "The Chromium engine" (installation-and-deployment.md) and "Installation" (installation-and-deployment.md) for details.

The Chromium engine renders **headless / off-screen** - no display server (X server / Xvfb) is required on Linux. The native PDF tool is statically linked. The Linux native package also bundles the Chromium shared-library closure the engine needs, so even a minimal Linux server or container image requires **no additional system packages** - see "Docker" (installation-and-deployment.md).

On **macOS** the engines link only macOS system frameworks, so there is no dependency closure to install and nothing to add beyond your application and the .NET runtime. The engine folder has the same shape as on the other platforms - one executable plus its files in a single directory, with no application bundle involved. macOS support requires **macOS 13 (Ventura) or newer on Apple Silicon**; see the note on Intel Macs in "Installation" (installation-and-deployment.md).

> **Important:** On Linux and macOS, the engine binaries must be executable. When a deployment is unzipped from a Windows-produced archive (which loses the executable bit), SelectPdf restores it automatically before launching the engine.

### Deployment to a Shared Hosting Environment

SelectPdf starts processes of its own: the Chromium engine runs its renderer and helper processes (and, with `ChromiumEngineHostMode.OutOfProcess`, an engine process per conversion), and the PDF to text and PDF to image converters run a native PDF tool. If you deploy to a shared hosting environment, confirm with your hosting company that starting processes and file access to the application output directory are permitted. If they are not, consider a dedicated or virtual server.

### Cloud deployments

SelectPdf runs on Microsoft Azure (App Service, Functions, Container Apps), Amazon Web Services (Lambda, Elastic Beanstalk) and Google Cloud (Cloud Run), on Windows and on Linux. See "Cloud deployments" (installation-and-deployment.md) for the supported platforms and a step-by-step page for each of them.

### Fonts on Linux

A Linux server or container image often has no fonts, and the fonts built into the library cover Latin, Greek and Cyrillic text only. Reference the **SelectPdf.Universal.Fonts** package to render every language, and put your own fonts in a **fonts** folder of the application. See "Fonts and languages" (installation-and-deployment.md).

### Deployment with Docker

SelectPdf runs in Docker containers - Linux and Windows - with no extra installation steps in the image: no system packages, no display server, and no special `docker run` flags. See "Docker" (installation-and-deployment.md) for base-image guidance, example Dockerfiles and the ready-to-run Docker samples.

### Native Debug Symbols

SelectPdf uses SkiaSharp for images and fonts. SkiaSharp 3.119.2 and later include the debug symbols of their native Windows libraries as runtime files - **libSkiaSharp.pdb**, about 85 MB for each Windows architecture, and **libHarfBuzzSharp.pdb** - so NuGet copies them into the **runtimes** folder of the application, about 300 MB in total. This applies to Linux deployments too when the application is published without a runtime identifier, because such a publish carries the files of every platform. The application never uses them (see [SkiaSharp issue 3519](https://github.com/mono/SkiaSharp/issues/3519)).

The **SelectPdf.Universal** package leaves these two files out of the build and publish output of the application, and out of its **.deps.json** file. To keep them, for example to analyse a crash dump of the native SkiaSharp library, set the **SelectPdfKeepNativeSymbols** property in the application project:

```xml
<PropertyGroup>
  <SelectPdfKeepNativeSymbols>true</SelectPdfKeepNativeSymbols>
</PropertyGroup>
```

> **Note:** Deleting the files after a build or publish is not a safe alternative: they stay listed in the application's .deps.json, and an ASP.NET Core application hosted in IIS has been reported to fail to start because of the missing files.

## Docker

Docker packages an application together with everything it needs to run into a portable container image, which then runs identically on a developer machine, an on-premise server or any cloud container service (Azure Container Apps / AKS, AWS ECS / EKS, Google Cloud Run, and so on).

**SelectPdf is container-ready**: reference the NuGet packages, publish, run — no extra installation steps inside the image. Both **Linux containers** and **Windows containers** are supported.

### Linux containers

Reference the managed **SelectPdf.Universal** package together with the **SelectPdf.Universal.Native.linux-x64** (or **linux-arm64**) package. `dotnet publish` places the native engines in the output automatically, and nothing else needs to be installed in the image:

- **No system packages / no `apt-get`** — the native Linux package bundles the complete Chromium shared-library closure (including the runtime-loaded NSS / SQLite modules required for HTTPS), and SelectPdf wires it up automatically when it launches the engine. The HTML-to-PDF and HTML-to-image converters run on the bare `mcr.microsoft.com/dotnet/aspnet` / `runtime` images.
- **No display server** — the Chromium engine renders headless / off-screen; no X server or Xvfb.
- **Fonts from a package, not from the image** — the base images have no fonts, and the fonts built into the library cover Latin, Greek and Cyrillic only. Reference the **SelectPdf.Universal.Fonts** package (Noto fonts for every script, copied into the publish output) to render every language - see "Fonts and languages" (installation-and-deployment.md).
- **No special `docker run` flags** — no `--no-sandbox`, no extra capabilities, no privileged mode. A plain `docker run` works.
- **No manual `chmod`** — the native package makes the engine executable when the application is published on Linux, and the library restores the permission before launching the engine if it was lost (see the note about non-root containers below).
- **No GPU and no `--shm-size` tuning required** — the engine renders in software, and Chromium does not keep its shared memory in `/dev/shm`, so Docker's default 64 MB is enough. The engine stays loaded inside the application between conversions (`GlobalProperties.ChromiumEngineHostMode`, the default) in a plain `docker run` as well.
- The PDF-to-text and PDF-to-image converters use a statically-linked native tool with no dependencies at all, and the rest of the library is fully managed.

**Base images:** any glibc-based .NET image works. Verified end-to-end (a real HTTPS page converted, with no packages installed): the default Debian 12 based `aspnet:8.0` / `runtime:8.0` tags, Ubuntu 22.04 (`8.0-jammy`), Ubuntu 24.04 (`10.0-noble`), and — via a self-contained publish — the RPM family (`amazonlinux:2023`, zero `dnf` packages). The engines require glibc 2.30 or newer — satisfied by every mainstream distribution since about 2020 (Ubuntu 20.04+, Debian 11+, RHEL 9+).

**Resources:** Chromium-based rendering benefits from CPU and memory — plan for at least 1 core and 2 GB of RAM per container, and prefer 2+ cores for consistently fast conversions under load.

A minimal multi-stage Dockerfile for an ASP.NET application using SelectPdf (any supported .NET version works the same way):

```text
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish MyApp.csproj -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app .
ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

Build and run:

```text
docker build -t myapp .
docker run --rm -p 8080:8080 myapp
```

> **Important:** Use a **glibc-based** Linux image (the default Debian / Ubuntu based `mcr.microsoft.com/dotnet` tags) for an application that runs the engines itself. **Alpine** (musl) images cannot run the native engines: an application on Alpine converts through the remote engine container instead - see "The remote engine" (installation-and-deployment.md).

#### Alternative: publish on the host, copy into the image

If restoring NuGet packages inside the Docker build is inconvenient (private feeds, offline build agents), publish on the host first — `dotnet publish -c Release -o publish` — and use a single-stage Dockerfile that just copies the publish output. The native engines are already in the publish folder, so the result is identical:

```text
FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY publish/ .
ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

#### Running the container as a non-root user

A non-root user needs nothing extra. When the application is published on Linux - inside the Docker build, as above - the native package makes the engine files readable and executable for every user, so the image can switch to the built-in `app` user before the entry point:

```text
USER app
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

An application published on Windows and copied into the image gets the same permissions from Docker, which marks files copied from a Windows build context as executable for every user. Verified: conversions run identically under the `app` user, on Docker and on platforms that pick the user themselves (AWS Lambda, Google Cloud Run functions).

#### Linux ARM64

For ARM64 containers, reference **SelectPdf.Universal.Native.linux-arm64** instead of **linux-x64** — everything else stays the same (the `mcr.microsoft.com/dotnet` base tags are multi-architecture, so the same image reference selects the matching architecture automatically). On an ARM64 host, build and run as usual; from an x64 host you can cross-build and run under emulation:

```text
docker build --platform linux/arm64 -t myapp-arm64 .
docker run --rm --platform linux/arm64 -p 8080:8080 myapp-arm64
```

> **Note:** This is also the package to use when building on an Apple Silicon Mac. Docker Desktop for Mac runs **Linux** containers, so a container image needs **SelectPdf.Universal.Native.linux-arm64** - not **osx-arm64**, which is for applications running natively on macOS.

### Windows containers

Reference the managed package together with **SelectPdf.Universal.Native.win-x64** and base the runtime image on **Windows Server Core** — for example `mcr.microsoft.com/dotnet/aspnet:8.0-windowsservercore-ltsc2022`. Server Core provides the GDI components the Chromium engine uses; the smaller **Nano Server** images do not and are not supported. No Visual C++ Redistributable is required — the native engines are static builds.

One container-specific step is required in the image (illustrated by the shipped Docker samples and verified end-to-end): **install the core Windows font families**. Server Core images ship almost no fonts, and the Chromium engine cannot start unless standard Windows font families are installed (third-party fonts alone are not enough; Segoe UI, Arial, Times New Roman, Verdana or Tahoma are). Copy them from Microsoft's full Windows Server image in a build stage and register them:

```text
FROM mcr.microsoft.com/windows/server:ltsc2022 AS fonts
# ... in the runtime stage:
COPY --from=fonts ["C:/Windows/Fonts/segoeui.ttf", "C:/Windows/Fonts/segoeuib.ttf", \
                   "C:/Windows/Fonts/arial.ttf", "C:/Windows/Fonts/arialbd.ttf", \
                   "C:/Windows/Fonts/"]
RUN powershell -Command "Get-ChildItem C:\Windows\Fonts -Filter *.ttf | \
    ForEach-Object { New-ItemProperty \
    -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Fonts' \
    -Name ($_.BaseName + ' (TrueType)') -Value $_.Name \
    -PropertyType String -Force | Out-Null }"
```

Add the italic variants and any other families your pages use the same way. Alternatively, base the runtime on the **full Windows Server image** (`mcr.microsoft.com/windows/server:ltsc2022`, which ships the complete font set) and install the ASP.NET Core runtime in the Dockerfile — no font step is needed there, at the cost of a larger image.

Nothing else is required: the engine detects that it is running in a Windows container and renders in software, which is all PDF and image output needs.

Windows container images can only be built and run on a Windows host with Docker switched to Windows-container mode. When Windows itself is not a requirement, prefer the Linux containers above — they are smaller and need no font step at all.

### Run-once (batch) containers

For batch conversion jobs, package a console application instead of a web application and mount a host folder to collect the produced files:

```text
docker run --rm -v "$PWD/out:/out" myconverter --url https://selectpdf.com -o /out/site.pdf
```

The container starts, performs the conversion, writes the PDF to the mounted folder and exits.

### Ready-to-run Docker samples

The official SelectPdf samples include complete Docker samples — multi-stage Dockerfiles (with `compose.yaml` files and per-sample instructions) that containerize the sample web application and a command-line converter, for both Linux and Windows containers. They are a working starting point for your own images.

The images you build are standard OCI images — push them to any container registry (Azure Container Registry, Amazon ECR, Docker Hub, GitHub Container Registry) and deploy them from your orchestrator as usual.

## Cloud deployments

SelectPdf runs on the main cloud platforms of Microsoft, Amazon and Google. The conversion code is the same everywhere; what differs is how the application is packaged and how much processor and memory the platform gives it. Each page below walks through one platform step by step, with the settings that matter and the results measured there.

### Supported platforms

| Platform | Operating system | How SelectPdf is deployed |
|---|---|---|
| "Azure App Service" (installation-and-deployment.md) | Windows, Linux | a web application (zip deploy or Web Deploy) |
| "Azure Functions" (installation-and-deployment.md) | Windows, Linux | a function app on a Dedicated (App Service) or Premium plan |
| "Azure Container Apps" (installation-and-deployment.md) | Linux | a container image |
| "AWS Lambda" (installation-and-deployment.md) | Linux (x64, arm64) | a container image |
| "AWS Elastic Beanstalk" (installation-and-deployment.md) | Windows (IIS), Linux | a source bundle of the web application |
| "Google Cloud Run" (installation-and-deployment.md) | Linux | a container image, or a Cloud Run function |

Each of them was verified in October 2026 with the same set of live web sites and with pages in English, Arabic, Hebrew, Hindi, Thai, Chinese, Japanese and Korean, every PDF compared with a conversion of the same page on a local Windows computer.

Any service that runs a Linux or Windows container also runs SelectPdf - see "Docker" (installation-and-deployment.md) - and so does any virtual machine (Azure Virtual Machines, Amazon EC2, Google Compute Engine).

### Not supported

- **Azure Functions Consumption and Flex Consumption plans** - see "Azure Functions" (installation-and-deployment.md).
- **Azure App Service Free and Shared plans** - they do not allow the engine processes.
- **Alpine (musl) Linux images running the engines themselves** - the native engines need a glibc-based distribution. An application on Alpine converts through the remote engine container instead: see "The remote engine" (installation-and-deployment.md).

### What every platform needs

- **The native package for the platform** - **SelectPdf.Universal.Native.linux-x64** (or **linux-arm64**) on Linux, **win-x64** (or **win-x86**) on Windows - next to the managed **SelectPdf.Universal** package. Nothing is installed on the server: no system packages, no display server, no `chmod`.
- **Fonts on Linux** - the **SelectPdf.Universal.Fonts** package; see "Fonts and languages" (installation-and-deployment.md).
- **Processor and memory** - HTML conversion runs a complete Chromium browser. Plan for 2 virtual CPUs and 2 to 4 GB of memory per instance; a heavy page needs more than 2 GB.
- **A writable, unpacked deployment** - the engine cannot be started from inside a mounted package (Azure's Run From Package on Windows, Flex Consumption).

An instance keeps the Chromium engine loaded between conversions, so a conversion on a warm instance takes about as long as on a developer computer (around 1.5 seconds for a typical web page). The first conversion of a new instance takes longer while the engine starts; platforms that stop idle instances (AWS Lambda, and container services scaled to zero) pay that again after a quiet period.

An instance converts up to `HtmlToPdfOptions.MaximumConcurrentConversions` pages at once (8 by default); set a platform's per-instance concurrency to the same number.

The conversion code itself does not change:

```csharp
// the conversion code is the same on every platform
HtmlToPdf converter = new HtmlToPdf();
PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");
byte[] pdf = doc.Save();
doc.Close();
```

```vbnet
' the conversion code is the same on every platform
Dim converter As New HtmlToPdf()
Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")
Dim pdf As Byte() = doc.Save()
doc.Close()
```

## Microsoft Azure

SelectPdf runs on Microsoft Azure on Windows and on Linux. Choose the service by how the application is built and how it is used:

### Choosing an Azure service

| Service | Use it for | SelectPdf |
|---|---|---|
| "Azure App Service" (installation-and-deployment.md) | web applications and APIs | Windows and Linux, Basic plan or above |
| "Azure Functions" (installation-and-deployment.md) | event-driven and HTTP functions | Dedicated (App Service) or Premium plan; not Consumption or Flex Consumption |
| "Azure Container Apps" (installation-and-deployment.md) | container images, scaling to zero | Linux containers |
| Azure Virtual Machines | full control of the server | Windows and Linux, no restrictions |
| Azure Kubernetes Service | orchestrated containers | the images described in "Docker" (installation-and-deployment.md) |

Azure Cloud Services (classic) was retired by Microsoft in August 2024, and Cloud Services (extended support) is scheduled for retirement in March 2027; use one of the services above for new deployments.

### Packages

Reference the managed **SelectPdf.Universal** package together with the native package of the platform the application runs on: **SelectPdf.Universal.Native.win-x64** for Windows, **SelectPdf.Universal.Native.linux-x64** for Linux. For Linux also reference **SelectPdf.Universal.Fonts** (see "Fonts and languages" (installation-and-deployment.md)).

## Azure App Service

SelectPdf runs on Azure App Service on Windows and on Linux, on the Basic plan or above, with the complete feature set. Free and Shared plans are not supported. The web application is deployed as it is: nothing is installed on the server and no setting is required.

### Packages

| App Service | Packages | Publish |
|---|---|---|
| Windows | **SelectPdf.Universal**, **SelectPdf.Universal.Native.win-x64** | Web Deploy or zip deploy |
| Linux | **SelectPdf.Universal**, **SelectPdf.Universal.Native.linux-x64**, **SelectPdf.Universal.Fonts** | zip deploy (Web Deploy is not available on Linux) |

The Linux image of App Service has no fonts for Chinese, Japanese and Korean, and a code deployment cannot install any, so reference the fonts package for Linux. A Linux application can be built and published from Windows: the native package carries every library the engine needs, and SelectPdf restores the engine's execute permission, which a zip deployment loses.

### Step by step

The steps use the Azure CLI; the Azure portal offers the same choices (Create a resource > Web App). An App Service plan is created for Windows or for Linux and cannot be changed later.

**1. Create a resource group and a plan.** Premium v3 gives the fastest conversions (see Performance below); add `--is-linux` for a Linux plan:

```text
az login
az group create --name selectpdf-rg --location westeurope
az appservice plan create --resource-group selectpdf-rg \
    --name selectpdf-plan --sku P1v3 --is-linux
```

**2. Create the web app** with the .NET 10 runtime - `DOTNETCORE:10.0` on Linux, `dotnet:10` on Windows:

```text
az webapp create --resource-group selectpdf-rg --plan selectpdf-plan \
    --name selectpdf-web --runtime "DOTNETCORE:10.0"
```

**3. Apply two settings.** **Always On** keeps the application loaded while idle, so the Chromium engine stays warm and a request never meets a starting worker. On Windows, also use a **64-bit** worker process: a new Windows web app starts as 32-bit.

```text
az webapp config set --resource-group selectpdf-rg --name selectpdf-web \
    --always-on true --use-32bit-worker-process false
```

**4. Publish and deploy** the application as a zip file:

```text
dotnet publish -c Release -o publish
cd publish && zip -r ../app.zip . && cd ..
az webapp deploy --resource-group selectpdf-rg --name selectpdf-web \
    --src-path app.zip --type zip
```

On Windows, create the zip with `Compress-Archive publish\* app.zip`. From Visual Studio, use Publish > Azure > Azure App Service (Windows or Linux) instead.

**5. Open the application** at `https://selectpdf-web.azurewebsites.net`.

### Do not use Run From Package on Windows

> **Important:** With the **WEBSITE_RUN_FROM_PACKAGE** setting, App Service mounts the deployment zip as wwwroot instead of extracting it, and the engine cannot be started from that mount. Every conversion then fails with *"Chromium engine process failed (exit code -1073741515) and no output JSON was produced"* - the Windows status STATUS_DLL_NOT_FOUND: the engine's libraries are in the package, but cannot be loaded from it.
> Remove the setting and deploy normally; zip deploy and Web Deploy extract the application into a writable wwwroot. In Visual Studio, leave **Run from package file** unselected in the publish profile.

If Run From Package has to stay, copy the engine folder (`Chromium-CEF-154.0.28`) to a writable folder such as `%HOME%\site\engine` when the application starts, and set `HtmlToPdfOptions.ChromiumEnginePath` to the copy. App Service for Linux ignores the setting for a code application, so Linux is not affected.

### Performance and choosing a plan

HTML conversion runs a complete browser and is processor bound, so the plan decides the speed:

| Plan | Conversion time |
|---|---|
| **Premium v3** (P1v3 and up) | reference: selectpdf.com in about 1.3 seconds on Linux and 1.7 seconds on Windows, once warm |
| **Basic** or **Standard** (for example S1) | about four times longer; heavy pages can reach the 60 second navigation timeout |

Measured in September and October 2026 in West Europe. An S1 instance has one comparatively slow virtual core; choose Premium v3 when conversion time matters, and Basic or Standard for occasional or background conversions.

On Windows, every conversion pays a fixed graphics start-up cost unless `HtmlToPdfOptions.WebGlEnabled` is False, which is the default. Leave it off unless your pages draw with WebGL.

### Windows worker bitness

A 32-bit worker process can use the win-x64 package: the engine then runs in a process of its own (slower, about 3.7 seconds for selectpdf.com on P1v3). With the **win-x86** package a 32-bit worker keeps the engine loaded. A 64-bit worker with win-x64 is the recommended combination.

### Sample

Deploy the web sample of the official samples (`windows/Web` or `linux/Web`) to try it: it converts pages, HTML and files with every option of the library.

## Azure Functions

SelectPdf runs in Azure Functions (version 4, .NET isolated worker) on Windows and on Linux, on a **Dedicated (App Service) plan** of Basic or above, or on an **Elastic Premium** plan.

### Hosting plans

| Plan | SelectPdf |
|---|---|
| Dedicated (App Service), Basic or above | supported, Windows and Linux |
| Elastic Premium (EP1 and up) | supported, Windows and Linux |
| Consumption (Windows) | **not supported** |
| Flex Consumption (Linux) | **not supported** |

A Windows Consumption plan cannot run the engine: the deployment does not fit the disk space the plan gives an application, Run From Package mounts the engine where it cannot be loaded, and the plan's sandbox stops the engine even when it is copied elsewhere. Flex Consumption always runs the application from the mounted, read-only package.

### Packages

The same packages as a web application: **SelectPdf.Universal** with **SelectPdf.Universal.Native.win-x64** on Windows, or with **SelectPdf.Universal.Native.linux-x64** and **SelectPdf.Universal.Fonts** on Linux.

### Step by step

**1. Create a resource group, a storage account and a plan** (`--is-linux` for Linux; `--sku EP1` for Elastic Premium):

```text
az login
az group create --name selectpdf-rg --location westeurope
az storage account create --resource-group selectpdf-rg \
    --name selectpdfstorage --sku Standard_LRS
az functionapp plan create --resource-group selectpdf-rg \
    --name selectpdf-fnplan --sku P1v3 --is-linux
```

**2. Create the function app** on that plan, with Always On (a Dedicated plan option):

```text
az functionapp create --resource-group selectpdf-rg \
    --name selectpdf-fn --plan selectpdf-fnplan \
    --storage-account selectpdfstorage --functions-version 4 \
    --runtime dotnet-isolated --runtime-version 10 --os-type Linux
az functionapp config set --resource-group selectpdf-rg \
    --name selectpdf-fn --always-on true
```

On Windows use `--os-type Windows` and also pass `--use-32bit-worker-process false` to the second command.

**3. Publish and deploy**:

```text
dotnet publish -c Release -o publish
cd publish && zip -r ../fn.zip . && cd ..
az functionapp deployment source config-zip --resource-group selectpdf-rg \
    --name selectpdf-fn --src fn.zip
```

A function app on a Dedicated plan does not use Run From Package, and the zip deployment does not turn it on, so the engine runs from a writable folder as in a web application. Do not set **WEBSITE_RUN_FROM_PACKAGE** (see "Azure App Service" (installation-and-deployment.md)).

### The function

A function that converts the page given in the query string:

```csharp
[Function("Convert")]
public static HttpResponseData Run(
    [HttpTrigger(AuthorizationLevel.Function, "get")] HttpRequestData req)
{
    string url = HttpUtility.ParseQueryString(req.Url.Query)["url"];
    HtmlToPdf converter = new HtmlToPdf();
    PdfDocument doc = converter.ConvertUrl(url);
    byte[] pdf = doc.Save();
    doc.Close();

    HttpResponseData response = req.CreateResponse(HttpStatusCode.OK);
    response.Headers.Add("Content-Type", "application/pdf");
    response.Body.Write(pdf, 0, pdf.Length);
    return response;
}
```

```vbnet
<[Function]("Convert")>
Public Shared Function Run(
    <HttpTrigger(AuthorizationLevel.Function, "get")> req As HttpRequestData
) As HttpResponseData
    Dim url As String = HttpUtility.ParseQueryString(req.Url.Query)("url")
    Dim converter As New HtmlToPdf()
    Dim doc As PdfDocument = converter.ConvertUrl(url)
    Dim pdf As Byte() = doc.Save()
    doc.Close()

    Dim response As HttpResponseData = req.CreateResponse(HttpStatusCode.OK)
    response.Headers.Add("Content-Type", "application/pdf")
    response.Body.Write(pdf, 0, pdf.Length)
    Return response
End Function
```

The official samples include a complete Azure Functions project for Windows and for Linux (`windows/Functions`, `linux/Functions`).

### What to expect

Function apps run on the same machines as web applications, so the times of "Azure App Service" (installation-and-deployment.md) apply: on P1v3 a warm instance converts selectpdf.com in under 2 seconds, and Basic or Standard plans take several times longer.

## Azure Container Apps

Azure Container Apps runs container images and scales them with the traffic, down to zero when idle. SelectPdf runs there in a Linux image built from the standard `mcr.microsoft.com/dotnet/aspnet` image, as its non-root user, with nothing installed.

### The image

Reference the three packages in the application:

```xml
<ItemGroup>
  <PackageReference Include="SelectPdf.Universal" Version="26.5.0" />
  <PackageReference Include="SelectPdf.Universal.Native.linux-x64"
                    Version="26.5.0" />
  <PackageReference Include="SelectPdf.Universal.Fonts" Version="26.5.0" />
</ItemGroup>
```

and publish it into a .NET image:

```text
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish MyApp.csproj -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:10.0
WORKDIR /app
COPY --from=build /app .
USER app
EXPOSE 8080
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

See "Docker" (installation-and-deployment.md) for more about images.

### Step by step

**1. Prepare the subscription** (once):

```text
az login
az extension add --name containerapp --upgrade
az provider register --namespace Microsoft.App --wait
az provider register --namespace Microsoft.OperationalInsights --wait
```

**2. Create a resource group and a container registry, and push the image** (the registry name is global):

```text
az group create --name selectpdf-rg --location westeurope
az acr create --resource-group selectpdf-rg --name selectpdfregistry \
    --sku Basic --admin-enabled true
az acr login --name selectpdfregistry
docker build --platform linux/amd64 -t selectpdfregistry.azurecr.io/myapp:1 .
docker push selectpdfregistry.azurecr.io/myapp:1
```

**3. Create the environment** and wait until it reports `Succeeded`, which can take several minutes:

```text
az containerapp env create --name selectpdf-env \
    --resource-group selectpdf-rg --location westeurope
az containerapp env show --name selectpdf-env --resource-group selectpdf-rg \
    --query properties.provisioningState
```

**4. Create the container app**:

```text
az containerapp create --name selectpdf-app --resource-group selectpdf-rg \
    --environment selectpdf-env \
    --image selectpdfregistry.azurecr.io/myapp:1 \
    --registry-server selectpdfregistry.azurecr.io \
    --target-port 8080 --ingress external --cpu 2 --memory 4Gi \
    --min-replicas 0 --max-replicas 3 \
    --scale-rule-name http --scale-rule-type http \
    --scale-rule-http-concurrency 8
```

- `--cpu 2 --memory 4Gi` is the largest consumption size; Chromium needs both.
- `--scale-rule-http-concurrency 8` starts another replica when one is converting 8 pages, the number SelectPdf converts at once by default.
- `--min-replicas 0` scales to zero when idle, and the first request then waits about 20 seconds for a replica to start. Use `--min-replicas 1` to keep one ready.

### What to expect

Measured in October 2026 (West Europe, consumption profile, 2 vCPU / 4 GiB): every conversion succeeded, every language rendered, and a warm replica converted selectpdf.com in about 1.3 seconds.

### Sample

The official samples include this deployment, with a step-by-step README, in the `cloud/azure-container-apps` folder of the edition's samples.

## Amazon Web Services

SelectPdf runs on Amazon Web Services on Linux (x64 and arm64/Graviton) and on Windows.

### Choosing an AWS service

| Service | Use it for | SelectPdf |
|---|---|---|
| "AWS Lambda" (installation-and-deployment.md) | functions, paying per conversion | a container image, x64 or arm64 |
| "AWS Elastic Beanstalk" (installation-and-deployment.md) | web applications on managed servers | Windows Server (IIS) and Amazon Linux 2023 |
| Amazon ECS, EKS, App Runner | container images | the images described in "Docker" (installation-and-deployment.md) |
| Amazon EC2 | full control of the server | Windows and Linux, no restrictions |

On Linux, reference **SelectPdf.Universal.Native.linux-x64** (or **linux-arm64** for Graviton) and **SelectPdf.Universal.Fonts**; on Windows, **SelectPdf.Universal.Native.win-x64**.

### AWS free plan accounts

An account on the AWS free plan can start only the instance types the plan allows. Elastic Beanstalk and EC2 then refuse types such as `t3.large` (the environment never gets an instance); `m7i-flex.large` (2 vCPU, 8 GB) is allowed and suits SelectPdf.

## AWS Lambda

SelectPdf runs in AWS Lambda as a function packaged in a **container image**, on x86_64 and on arm64. A zip deployment is not possible: the Chromium engine and its libraries are larger than the 250 MB a zip-based function can hold.

### The function and its image

Use the Lambda .NET base image and the packages for the function's architecture:

```xml
<ItemGroup>
  <PackageReference Include="Amazon.Lambda.Core" Version="3.3.0" />
  <PackageReference Include="Amazon.Lambda.APIGatewayEvents" Version="3.0.1" />
  <PackageReference Include="Amazon.Lambda.Serialization.SystemTextJson"
                    Version="3.0.1" />
  <PackageReference Include="SelectPdf.Universal" Version="26.5.0" />
  <PackageReference Include="SelectPdf.Universal.Native.linux-x64"
                    Version="26.5.0" />
  <PackageReference Include="SelectPdf.Universal.Fonts" Version="26.5.0" />
</ItemGroup>
```

```text
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish MyFunction.csproj -c Release -r linux-x64 \
    --self-contained false -o /app

FROM public.ecr.aws/lambda/dotnet:10
COPY --from=build /app ${LAMBDA_TASK_ROOT}
CMD ["MyFunction::MyFunction.Function::Handler"]
```

The function runs as Lambda's own non-root user; the native package makes the engine executable for every user when the image is built, so the Dockerfile needs no `chmod`. Lambda images have no fonts, which is why the fonts package is referenced.

A handler that converts the page given in the query string of a function URL or HTTP API request:

```csharp
public APIGatewayHttpApiV2ProxyResponse Handler(
    APIGatewayHttpApiV2ProxyRequest request, ILambdaContext context)
{
    HtmlToPdf converter = new HtmlToPdf();
    PdfDocument doc = converter.ConvertUrl(request.QueryStringParameters["url"]);
    byte[] pdf = doc.Save();
    doc.Close();
    return new APIGatewayHttpApiV2ProxyResponse
    {
        StatusCode = 200,
        Headers = new Dictionary<string, string> {
            ["Content-Type"] = "application/pdf" },
        Body = Convert.ToBase64String(pdf),
        IsBase64Encoded = true
    };
}
```

```vbnet
Public Function Handler(request As APIGatewayHttpApiV2ProxyRequest,
                        context As ILambdaContext
) As APIGatewayHttpApiV2ProxyResponse
    Dim converter As New HtmlToPdf()
    Dim doc As PdfDocument =
        converter.ConvertUrl(request.QueryStringParameters("url"))
    Dim pdf As Byte() = doc.Save()
    doc.Close()
    Return New APIGatewayHttpApiV2ProxyResponse With {
        .StatusCode = 200,
        .Headers = New Dictionary(Of String, String) From {
            {"Content-Type", "application/pdf"}},
        .Body = Convert.ToBase64String(pdf),
        .IsBase64Encoded = True
    }
End Function
```

### Step by step

You need the AWS CLI (`aws configure`) and Docker. The commands use region `eu-north-1` and account `123456789012`; replace both.

**1. Build the image.** `--provenance=false --sbom=false` matter: Lambda refuses the multi-manifest images Docker builds by default ("The image manifest, config or layer media type ... is not supported").

```text
docker build --platform linux/amd64 --provenance=false --sbom=false \
    -t selectpdf-lambda .
```

**2. Push it to Amazon ECR:**

```text
aws ecr create-repository --repository-name selectpdf-lambda
aws ecr get-login-password | docker login --username AWS \
    --password-stdin 123456789012.dkr.ecr.eu-north-1.amazonaws.com
docker tag selectpdf-lambda \
    123456789012.dkr.ecr.eu-north-1.amazonaws.com/selectpdf-lambda:1
docker push 123456789012.dkr.ecr.eu-north-1.amazonaws.com/selectpdf-lambda:1
```

**3. Create an execution role** - in the console, IAM > Roles > Create role > AWS service > Lambda, with the **AWSLambdaBasicExecutionRole** policy.

**4. Create the function** with 2048 MB of memory (Lambda gives processor time in proportion to memory) and a 120 second timeout:

```text
IMAGE=123456789012.dkr.ecr.eu-north-1.amazonaws.com/selectpdf-lambda:1
aws lambda create-function --function-name selectpdf-lambda \
    --package-type Image --memory-size 2048 --timeout 120 \
    --code ImageUri=$IMAGE \
    --role arn:aws:iam::123456789012:role/selectpdf-lambda-role
```

**5. Give it a URL** (`AWS_IAM` requires signed requests; `NONE` makes it public):

```text
aws lambda create-function-url-config --function-name selectpdf-lambda \
    --auth-type AWS_IAM
```

**arm64 (Graviton)**: reference **SelectPdf.Universal.Native.linux-arm64**, publish with `-r linux-arm64`, build with `--platform linux/arm64` and create the function with `--architectures arm64`. arm64 is cheaper per second and converts at a similar speed.

### Response size

A Lambda response is limited to 6 MB and the PDF travels base64-encoded in it, so about 4.4 MB of PDF fits. Store larger documents in an Amazon S3 bucket and return a presigned link instead; the sample does this when its `PDF_BUCKET` environment variable names a bucket.

### What to expect

Measured in October 2026 (eu-north-1, 2048 MB): every conversion succeeded on x86_64 and arm64, with every language rendered and the pages matching a Windows conversion. A warm function converts selectpdf.com in about 1.5 seconds (x86_64) to 2.3 seconds (arm64).

Lambda replaces an environment after a few idle minutes, and the first conversion of a new environment takes 10 to 50 seconds while Chromium starts. Provisioned concurrency keeps environments ready when that matters.

### Sample

The official samples include this deployment, with a step-by-step README, in the `cloud/aws-lambda` folder of the edition's samples.

## AWS Elastic Beanstalk

AWS Elastic Beanstalk runs a web application on EC2 instances it manages. SelectPdf runs on both of its .NET platforms: **Windows Server running IIS** and **Amazon Linux 2023 running .NET**.

### The source bundle

Elastic Beanstalk deploys a zip file, the source bundle, whose shape depends on the platform.

**Windows Server / IIS.** Publish the application for `win-x64` - self-contained, so it does not depend on the .NET versions the platform installs - zip the publish folder as `site.zip`, and put it in the bundle next to an `aws-windows-deployment-manifest.json` that installs it as the root site:

```text
{
  "manifestVersion": 1,
  "deployments": {
    "aspNetCoreWeb": [ {
      "name": "myapp",
      "parameters": {
        "appBundle": "site.zip",
        "iisPath": "/",
        "iisWebSite": "Default Web Site"
      }
    } ]
  }
}
```

**Amazon Linux 2023.** Publish the application (framework-dependent, for the ".NET 10" platform) with the fonts package, add a `Procfile` with the command that starts it - the platform's nginx forwards to port 5000 - and zip the folder:

```text
web: dotnet MyApp.dll --urls http://0.0.0.0:5000
```

Create the zip with forward slashes in the paths (Linux reads a backslash as part of a file name): `zip -r`, or the sample's `build-bundles.ps1` on Windows.

### Step by step

1. In the AWS console, open Elastic Beanstalk > Create application.
2. Platform: **.NET on Windows Server** (Windows Server 2022, IIS 10.0) or **.NET 10 running on 64bit Amazon Linux 2023**.
3. Application code: Upload your code, and choose the bundle.
4. Presets: Single instance for a test, High availability for production.
5. Service access: let the console create the service role and the EC2 instance profile if you have none.
6. Instance type: at least 2 vCPU and 8 GB - for example `m7i-flex.large`, `m7i.large` or `t3.large` (on a free plan account, see "Amazon Web Services" (installation-and-deployment.md)).
7. Create the environment, wait until its health is Ok, and open its domain.

With the CLI, upload the bundle to S3 and use `aws elasticbeanstalk create-application-version` and `create-environment`; the sample's README has the commands.

### What to expect

Measured in October 2026 (eu-north-1, single m7i-flex.large instance): every conversion succeeded on both platforms, and the Windows output is identical to a conversion on a local Windows computer. The instance keeps the engine loaded, so selectpdf.com converts in about 1.1 seconds on Linux and 1.5 seconds on Windows; the first conversion after a deployment takes longer (about 8 seconds on Windows, 3 on Linux).

### Sample

The official samples include this deployment, with a step-by-step README, in the `cloud/aws-elastic-beanstalk` folder of the edition's samples. Its `build-bundles.ps1` and `build-bundles.sh` publish the web sample into both bundles.

## Google Cloud Run

Google Cloud Run runs container images and scales them with the traffic. SelectPdf runs there in a Linux image built from the standard `mcr.microsoft.com/dotnet/aspnet` image, as its non-root user, in both execution environments, and also as a **Cloud Run function**.

### The image

Reference the three packages in the application:

```xml
<ItemGroup>
  <PackageReference Include="SelectPdf.Universal" Version="26.5.0" />
  <PackageReference Include="SelectPdf.Universal.Native.linux-x64"
                    Version="26.5.0" />
  <PackageReference Include="SelectPdf.Universal.Fonts" Version="26.5.0" />
</ItemGroup>
```

and build the image as in "Azure Container Apps" (installation-and-deployment.md) or "Docker" (installation-and-deployment.md); Cloud Run passes the port in the `PORT` environment variable (8080 by default).

### Step by step

**1. Set up the project** (once): create a project in the Google Cloud console, link a billing account, install the gcloud CLI, then:

```text
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
gcloud config set run/region europe-north1
gcloud services enable run.googleapis.com artifactregistry.googleapis.com
gcloud artifacts repositories create selectpdf \
    --repository-format=docker --location=europe-north1
gcloud auth configure-docker europe-north1-docker.pkg.dev
```

**2. Build and push the image:**

```text
docker build --platform linux/amd64 \
    -t europe-north1-docker.pkg.dev/YOUR_PROJECT_ID/selectpdf/myapp .
docker push europe-north1-docker.pkg.dev/YOUR_PROJECT_ID/selectpdf/myapp
```

**3. Deploy:**

```text
gcloud run deploy myapp \
    --image europe-north1-docker.pkg.dev/YOUR_PROJECT_ID/selectpdf/myapp \
    --cpu 2 --memory 4Gi --timeout 300 --concurrency 8 \
    --no-allow-unauthenticated
```

- `--cpu 2 --memory 4Gi` - with 2 GiB a heavy page can run the instance out of memory; Cloud Run then restarts it and the request fails with HTTP 503.
- `--concurrency 8` - the number of pages SelectPdf converts at once by default.
- `--no-allow-unauthenticated` - callers need an identity token; use `--allow-unauthenticated` only for a public service.
- `--execution-environment gen2` is optional: SelectPdf runs in the default (first generation) environment and in the second generation alike.

### Cloud Run functions

The same code runs as a Cloud Run function (`gcloud functions deploy --gen2 --runtime dotnet8`, with the **Google.Cloud.Functions.Hosting** package and an `IHttpFunction` class). Google builds the function from source, restoring the SelectPdf packages like any other, and runs it on Ubuntu as a non-root user. Use at least `--memory 2Gi` and `--cpu 2`.

### What to expect

Measured in October 2026 (europe-north1, 2 vCPU): every conversion succeeded in both execution environments and as a function, with every language rendered. A warm instance converts selectpdf.com in about 1.4 seconds; the first conversion of a new instance takes 5 to 10 seconds. Cloud Run keeps an idle instance for a while, and `--min-instances 1` keeps one ready. A response can be up to 32 MB, so large PDFs are returned directly.

### Sample

The official samples include this deployment, with a step-by-step README, in the `cloud/google-cloud-run` folder of the edition's samples.
