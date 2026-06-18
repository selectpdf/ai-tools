# Deployment, Azure, performance and troubleshooting

How to deploy the commercial Select.Pdf .NET library to Windows servers and Microsoft Azure, apply the license key, harden it against untrusted HTML, and diagnose the most common conversion failures.

## Deployment basics

To deploy a project that uses Select.Pdf, three files must be deployed together: **Select.Pdf.dll**, **Select.Html.dep** and **Select.Tools.dep**. There are no additional setups or configurations needed on the server or the client machine. Because `Select.Html.dep` and `Select.Tools.dep` are not referenced by the project, they may not be copied to the deployment destination automatically — if they are missing, copy them manually.

If the Blink rendering engine is used (works only with the .NET Core version and with .NET Framework 4.6.1 or above), the Blink binaries (the full Chromium folder) must also be deployed. If the Chromium rendering engine is used, the Chromium runtime folder installed by the matching **Chromium.Windows** NuGet package must also be deployed alongside the application; when the project references that package, MSBuild places the folder in the application output directory automatically at build time, so a standard publish picks it up.

**Full trust is required** on the deployment machine. Select.Pdf makes numerous native API calls, so it requires full trust to run. On a shared hosting environment, confirm with the hosting company that full trust is supported; if it cannot be granted, move the site to a dedicated or virtual server.

### x86 / x64 and process bitness

Select.Pdf ships x86 (default), x64-optimized, and AnyCPU packages, and makes native calls — so the native binaries must match the bitness of the process that loads them. The x64 optimized version allows allocation of much more memory than the default x86 version, which is the recommended remedy for large-image out-of-memory conversions on a 64-bit system (see Troubleshooting below).

Well-established platform facts to keep deployment consistent (not Select.Pdf-specific APIs):

- Match the package bitness to the process: load the x86 native package in a 32-bit process and the x64 package in a 64-bit process. A mismatch surfaces as `System.BadImageFormatException` when the native DLL is loaded.
- Under IIS, the process bitness is set by the application pool: **Enable 32-Bit Applications = True** runs the worker as x86; `False` runs it as x64. The Select.Pdf package you deploy must match that setting.
- The same license key works across all packages and runtimes (.NET 10 / 9 / 8 / 7 / 6 / 5 / .NET Core / .NET 4.6.1 / 4.7.2 / 4.0–4.5 / 2.0 / AnyCPU / x64), so switching bitness does not require a new key.

### GAC installation

If Select.Pdf is installed into the GAC, only **Select.Pdf.dll** needs to be added; the related `Select.Html.dep` and `Select.Tools.dep` are automatically copied to the GAC as linked resources.

## Deploying on Microsoft Azure

Azure offers several deployment targets; how Select.Pdf behaves depends on which one you pick.

- **Azure Virtual Machines** — Select.Pdf works without any restrictions.
- **Azure Cloud Services** (Web Roles) — Select.Pdf works without any restrictions.
- **Azure Web Apps / App Service** — runs in a sandbox that blocks most User32/GDI32 (Win32k.sys) system calls. Because Select.Pdf's regular rendering engine performs many GDI calls, the default engine does not work there. Two options:
  - **Restricted rendering engine** — starting with v18.3, Select.Pdf supports Azure Web Apps on **Windows**, **Basic plan or above** (does not work with Free/Shared plans). Standard/Premium plans make conversions faster. The restricted engine has limitations: no web fonts, only single-page `PdfHtmlElement` objects, and no support for excluding elements from conversion. It is controlled by two `GlobalProperties` properties: `EnableRestrictedRenderingEngine` and `EnableFallbackToRestrictedRenderingEngine`. By default the restricted engine is disabled, but fallback to it is enabled on the Azure Web Apps platform, so with default settings the restricted engine activates automatically there. To force it on other platforms (e.g. a dev machine), set `GlobalProperties.EnableRestrictedRenderingEngine = true`.
  - **Chromium engine (recommended for new Azure Web Apps projects)** — starting with v26.2, the Chromium rendering engine runs on Azure Web Apps (Basic plan or above) with the **full feature set** — no restrictions. Install the matching **Chromium.Windows** companion NuGet package alongside the main package, then set `HtmlToPdfOptions.RenderingEngine = RenderingEngine.Chromium`. Choose Chromium over the restricted engine when you need web fonts, multi-page `PdfHtmlElement` objects, element exclusion, or modern HTML5/CSS3/ES2020+ rendering.
- **Azure Functions** — supported starting with Azure Functions SDK 3.0, using the restricted rendering engine (same limitations as above). The hosting plan must be Premium or an App Service Plan (Basic or above); it does **not** work with a Consumption plan, nor with Free/Shared App Service plans.

> Older Select.Pdf versions only work on Azure if deployed to a Cloud Service or Virtual Machine — a "Navigation timeout" on an Azure Website can simply be an old library version on an unsupported target.

### Native engine log for Azure diagnostics

When using the Blink/Chromium native engine, set `HtmlToPdfOptions.LFN` to a writable file path to capture the native engine log. This is useful for diagnosing conversion problems on Azure where the engine's own errors are otherwise not visible.

```csharp
HtmlToPdf converter = new HtmlToPdf();
converter.Options.RenderingEngine = RenderingEngine.Chromium;

// write the native engine log to a file for diagnostics
converter.Options.LFN = @"D:\home\LogFiles\selectpdf-engine.log";

PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");
doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()
converter.Options.RenderingEngine = RenderingEngine.Chromium

' write the native engine log to a file for diagnostics
converter.Options.LFN = "D:\home\LogFiles\selectpdf-engine.log"

Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")
doc.Save("output.pdf")
doc.Close()
```

## Performance and concurrency on servers

The conversion launches a new process that renders the HTML, so the application must have enough rights to start processes (an environment that blocks this raises error 1260 — see Troubleshooting). Use one `HtmlToPdf` converter instance per conversion rather than sharing a single instance across threads.

For throughput on a 64-bit server, prefer the x64 package: it allows allocating far more memory than the x86 default, which matters when converting pages with many or very large images. On Azure Web Apps, a Standard or Premium plan makes restricted-engine conversions faster than Basic.

Tune per-conversion timing through `HtmlToPdfOptions`:

```csharp
HtmlToPdf converter = new HtmlToPdf();

// navigation timeout, seconds (default 60)
converter.Options.MaxPageLoadTime = 120;

// optional pre-render delay so slow assets (e.g. web fonts) finish loading
converter.Options.MinPageLoadTime = 2;
```

```vbnet
Dim converter As New HtmlToPdf()

' navigation timeout, seconds (default 60)
converter.Options.MaxPageLoadTime = 120

' optional pre-render delay so slow assets (e.g. web fonts) finish loading
converter.Options.MinPageLoadTime = 2
```

> Faithfulness note: the sources for this library do not document a `MaximumConcurrentConversions` option; do not assume one exists. Control concurrency at the application level (e.g. your own queue/semaphore) and one converter instance per conversion.

## Applying the license key

Set the license key once at startup, before any conversion, via `GlobalProperties.LicenseKey`. The free trial is a full version that watermarks every page; setting a valid key removes the watermark. The same key works across all versions and packages.

```csharp
// set once at application startup
SelectPdf.GlobalProperties.LicenseKey = "your-license-key";
```

```vbnet
' set once at application startup
SelectPdf.GlobalProperties.LicenseKey = "your-license-key"
```

The Extras package requires its own key, set the same way on its own `GlobalProperties` type:

```csharp
SelectPdf.Extras.GlobalProperties.LicenseKey = "your-license-key";
```

```vbnet
SelectPdf.Extras.GlobalProperties.LicenseKey = "your-license-key"
```

> If `LicenseKey` cannot be found on `GlobalProperties`, the project is referencing the Community Edition (`Select.HtmlToPdf.dll`) instead of the full commercial library (`Select.Pdf.dll`). Reference `Select.Pdf.dll`.

## Security recommendations

When converting HTML you control, no special steps are needed. When converting external/untrusted HTML, apply these:

- **Control input** — inspect the page content (especially JavaScript) for anything that might harm the system; sanitizing user input is hard and attackers can work around protections.
- **Restrict access** — if the system is only used from known locations, limit the allowed IP addresses.
- **Disable JavaScript execution** — if scripts are not needed, turn them off:

  ```csharp
  converter.Options.JavaScriptEnabled = false;
  ```

  ```vbnet
  converter.Options.JavaScriptEnabled = False
  ```

- **Disable access to local files** — if the conversion does not need local server files, deny access. Use `GlobalProperties.ForceDenyLocalFileAccess` to enforce it globally, or `HtmlToPdfOptions.DenyLocalFileAccess` per converter:

  ```csharp
  SelectPdf.GlobalProperties.ForceDenyLocalFileAccess = true;
  // or, per conversion:
  converter.Options.DenyLocalFileAccess = true;
  ```

  ```vbnet
  SelectPdf.GlobalProperties.ForceDenyLocalFileAccess = True
  ' or, per conversion:
  converter.Options.DenyLocalFileAccess = True
  ```

## Troubleshooting common errors

| Symptom | Cause | Fix |
| --- | --- | --- |
| `System.BadImageFormatException` when the native DLL loads | Process bitness does not match the Select.Pdf native package (x86 package in a 64-bit process or vice versa) | Match the package to the process bitness; under IIS set the app pool's **Enable 32-Bit Applications** to match (well-established platform fact, consistent with the Deployment page) |
| "Conversion failure. Could not find 'Select.Html.dep'." | `Select.Html.dep` / `Select.Tools.dep` were not copied to the deployment destination (no project reference) | Copy `Select.Html.dep` and `Select.Tools.dep` manually next to `Select.Pdf.dll` |
| `System.OutOfMemoryException` during conversion | Large web pages with many / very large images; images are converted to bitmaps before JPEG compression and consume a lot of memory | Optimize images for the web; on a 64-bit system use the x64-optimized package, which allows more memory |
| Conversion failure error 5 (`ERROR_ACCESS_DENIED`, "Access is denied.") | The engine file does not have execute permission | Enable execute permissions on `Select.Html.dep` |
| Conversion failure error 32 (`ERROR_SHARING_VIOLATION`, "used by another process.") | `Select.Html.dep` was left locked, likely after a file transfer | Re-upload `Select.Html.dep` |
| Conversion failure error 1260 (`ERROR_ACCESS_DISABLED_BY_POLICY`, "blocked by group policy") | The tool launches a new process to render the HTML and the application lacks the rights to do so | Grant the application enough rights to launch a process (relax the blocking group policy) |
| "Conversion error: Navigation timeout." | Page takes longer than the default 60 s to load; or the URL is not resolvable on the server; or a proxy is required; or an old library on an unsupported Azure target; or Manual startup mode where the JS trigger was never called | Increase `HtmlToPdfOptions.MaxPageLoadTime`; verify the server can resolve the URL; configure `HtmlToPdfOptions.ProxyOptions`; on Azure use a supported target/version; call the JS trigger if using Manual startup mode |
| Wrong fonts / gibberish text when web fonts are used | Web fonts not fully loaded, or in-memory font caching misbehaving on some systems (fixed in v17.1) | Add a delay with `converter.Options.MinPageLoadTime = 2;` and/or cache fonts on disk: `GlobalProperties.CacheFonts = true; GlobalProperties.FontsFolder = @"c:\path\";`. Provide web fonts as TTF or WOFF (the only supported types) |
| Styles not applied / images missing when converting an HTML string | Relative resource URLs cannot be resolved without a base URL | Use the `ConvertHtmlString(html, baseUrl)` overload so the converter can resolve relative URLs (`baseUrl + relative url = absolute url`) |
| An image is split across two PDF pages | Default behavior allows breaking inside an image | Set `HtmlToPdfOptions.KeepImagesTogether = true`, or add `style="page-break-inside: avoid"` on the `<img>` |
| "We're sorry, a general error has occurred." | Generic unhandled conversion error | Retry the operation; if it persists, enable the native engine log via `HtmlToPdfOptions.LFN` to capture the underlying error, and verify deployment files, permissions, and bitness |

See also: installation-and-engines.md, html-to-pdf.md, security-and-signatures.md
