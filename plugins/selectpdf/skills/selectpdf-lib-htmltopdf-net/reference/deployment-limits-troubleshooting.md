# Deployment, the 5-page limit, and troubleshooting (Community Edition)

How to ship Select.HtmlToPdf (free Community Edition, HTML→PDF only), what the 5-page cap means, and how to diagnose the common deployment failures.

## The 5-page limit

The Community Edition renders **at most 5 pages per document**. Any content that would fall beyond page 5 is dropped from the generated PDF — there is no error, the document is simply truncated at 5 pages.

This is an edition cap, not a converter option: there is no setting that raises it. For unlimited pages, upgrade to the commercial **Select.Pdf** library, which exposes the **same API** (the conversion code does not change — only the assembly/license).

## Deployment basics

To deploy a project that uses Select.Pdf HtmlToPdf Converter, include **Select.HtmlToPdf.dll** and **Select.Html.dep** with your project. There are no additional setups or configurations needed on the server or the client machine.

- **`Select.Html.dep` must sit in the same folder as `Select.HtmlToPdf.dll`.** Because `Select.Html.dep` has no project reference, it can fail to copy to the deployment destination — copy it manually if it is missing. Alternatively, point to it explicitly with the `HtmlEngineFullPath` property of the `GlobalProperties` class.
- **Full trust is required** on the deployment machine. Select.Pdf makes numerous native API calls and will not run under partial trust. On shared hosting, confirm with the host that full trust is granted; if not, move to a dedicated or virtual server.
- **Native dependency / bitness.** `Select.Html.dep` is a native (Windows) component. The package bitness must match the bitness of the process that loads it:
  - Use the **x86** package when the host process runs as 32-bit; use the **x64** package when it runs as 64-bit.
  - In IIS, this is governed by the **application pool's bitness** — the *Enable 32-Bit Applications* setting on the app pool. If 32-bit applications are enabled, the worker process is x86 and you must deploy the x86 package; otherwise it is x64 and you must deploy the x64 package.
  - A mismatch surfaces at conversion time as a `BadImageFormatException` (see troubleshooting below). The x64 build can also allocate more memory, which helps with large/image-heavy pages.
- **GAC install:** only `Select.HtmlToPdf.dll` needs to be added to the GAC; `Select.Html.dep` is copied alongside automatically as a linked resource.

Specifying the engine path explicitly:

```csharp
SelectPdf.GlobalProperties.HtmlEngineFullPath = @"C:\path\to\Select.Html.dep";
```

```vbnet
SelectPdf.GlobalProperties.HtmlEngineFullPath = "C:\path\to\Select.Html.dep"
```

## Deploying on Azure

Azure Web Apps run apps inside a sandbox that blocks most User32/GDI32 (Win32k.sys) calls. The default rendering engine performs many GDI calls, so it does **not** work on Azure Web Apps. Engine considerations:

- **Cloud Services (Web Roles)** and **Virtual Machines** run Select.HtmlToPdf with **no restrictions**.
- **Azure Web Apps** require **Windows** and an **App Service plan of Basic or above** (Free/Shared plans do not work). On this platform the restricted rendering engine is used. The restricted engine drops some features (no web fonts; some advanced HTML5/CSS3 rendering is reduced). It is controlled via `GlobalProperties.EnableRestrictedRenderingEngine` and `GlobalProperties.EnableFallbackToRestrictedRenderingEngine`; by default the restricted engine is off but the fallback to it is enabled on Azure Web Apps, so it activates automatically there.
- **Azure Functions** work starting with Azure Functions SDK 3.0, on a Premium or App Service plan (Basic or above) — not on a Consumption plan, nor on Free/Shared. The restricted engine is used here too, with the same feature reductions.

### Native engine log for diagnostics

For diagnosing conversion problems (notably on Azure, where the failure cause is often opaque), write the native engine log to a file via `HtmlToPdfOptions.LFN`:

```csharp
HtmlToPdf converter = new HtmlToPdf();
converter.Options.LFN = @"D:\home\LogFiles\selectpdf-engine.log";
```

```vbnet
Dim converter As New HtmlToPdf()
converter.Options.LFN = "D:\home\LogFiles\selectpdf-engine.log"
```

## License key

The Community Edition is **free** and may be used for personal or commercial purposes. It works **without a license key** — there is nothing to set. The CE `GlobalProperties` class does **not** expose a `LicenseKey` member at all; licensing is a commercial-library concept.

To remove the 5-page cap, upgrade to the commercial **Select.Pdf** library and set `SelectPdf.GlobalProperties.LicenseKey` there (see the `selectpdf-lib-net` skill). The converter API is otherwise identical, so existing conversion code carries over unchanged.

## Security recommendations

When converting HTML you control, the defaults are fine. When converting untrusted external HTML/code, apply these:

- **Control input.** Inspect the page being converted for harmful code (especially JavaScript) before converting. Sanitizing user input is hard and can be bypassed.
- **Restrict access.** If the service is only used from known locations, limit the allowed IP addresses.
- **Disable JavaScript execution** if you do not need it:

```csharp
converter.Options.JavaScriptEnabled = false;
```

```vbnet
converter.Options.JavaScriptEnabled = False
```

- **Disable access to local files** if the conversion does not need server files. This is a global setting:

```csharp
SelectPdf.GlobalProperties.ForceDenyLocalFileAccess = true;
```

```vbnet
SelectPdf.GlobalProperties.ForceDenyLocalFileAccess = True
```

  A per-conversion equivalent is also available on the options object via `converter.Options.DenyLocalFileAccess`.

## Troubleshooting common errors

| Symptom | Cause | Fix |
| --- | --- | --- |
| `BadImageFormatException` when loading the converter / `Select.Html.dep` | x86/x64 package does not match the process bitness | Deploy the package matching the process bitness; for IIS, align the app pool's *Enable 32-Bit Applications* setting with the deployed package |
| Conversion failure: *Could not find 'Select.Html.dep'* | `Select.Html.dep` was not copied to the deployment folder (no project reference) | Copy `Select.Html.dep` next to `Select.HtmlToPdf.dll`, or set `GlobalProperties.HtmlEngineFullPath` to its location |
| Conversion failure: `System.OutOfMemoryException` | Large pages with many / very large images (images are converted to bitmaps internally before JPEG compression) | Optimize images for the web; on x64 systems use the x64 package, which can allocate more memory |
| Conversion failure error 5 — `ERROR_ACCESS_DENIED` (`Access is denied`) | The engine binary lacks execute permission | Enable execute permission on `Select.Html.dep` |
| Conversion failure error 32 — `ERROR_SHARING_VIOLATION` (file in use by another process) | `Select.Html.dep` was left locked during file transfer | Re-upload `Select.Html.dep` |
| Conversion failure error 1260 — `ERROR_ACCESS_DISABLED_BY_POLICY` (blocked by group policy) | The converter launches a child process to render HTML and lacks the rights to do so | Grant the application enough rights to start a new process |
| Conversion error: *Navigation timeout* / blank or empty PDF | Page does not finish loading within the timeout | Increase the load/conversion timeout and/or set a minimum pre-render delay; ensure the target URL is reachable from the server |
| Wrong fonts / gibberish text when web fonts are used | Pre-v17.1 web-font rendering bug | Fixed in v17.1 — upgrade |
| Web fonts not applied at all | Font format unsupported | Provide **TTF** or **WOFF** versions — these are the only web-font types Select.Pdf supports |
| Converting an HTML string: styles not applied, images missing | Relative resource URLs cannot be resolved without a base path | Use the `ConvertHtmlString(html, baseUrl)` overload; `baseUrl` + relative URL = absolute URL |
| Generated PDF has more content but only **5 pages** appear | Community Edition 5-page cap (content past page 5 is dropped) | Reduce the document to ≤5 pages, or upgrade to the commercial Select.Pdf for unlimited pages (same API) |
| HTML→PDF does not run on Azure Web Apps | Default GDI-based engine is blocked by the Azure sandbox | Use Cloud Services or a VM (no restrictions), or run on Azure Web Apps with Basic+ plan on Windows where the restricted engine applies |

See also: installation-and-engines.md, html-to-pdf.md
