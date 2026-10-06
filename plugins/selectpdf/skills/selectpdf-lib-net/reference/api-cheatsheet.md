# API cheat sheet (Select.Pdf, commercial)

Quick lookup of the most-used types, members and enums in the commercial `SelectPdf` namespace, with a pointer to the reference file that covers each. Everything here is verified against the v26.3 SDK reference and, for the v26.4 additions, the SDK source; if a member isn't listed, confirm it in the source before using it.

```csharp
using SelectPdf;                 // core
using SelectPdf.Forms;           // AcroForms manager (Select.Pdf.Extras)
using SelectPdf.Compressor;      // PDF compressor (Select.Pdf.Extras)
```

## Global

| Member | Purpose |
|---|---|
| `GlobalProperties.LicenseKey` | Set once at startup to unlock the commercial library (trial mode if unset). |
| `GlobalProperties.HtmlEngineFullPath` | Explicit path to `Select.Html.dep` when it isn't copied next to the DLL. |
| `GlobalProperties.ForceDenyLocalFileAccess` | Block `file://` access globally (security). |
| `GlobalProperties.EnableRestrictedRenderingEngine` / `EnableFallbackToRestrictedRenderingEngine` | Restricted engine controls (Azure Web Apps). |

→ `installation-and-engines.md`, `deployment-azure-troubleshooting.md`

## HTML → PDF / image

| Type / member | Notes |
|---|---|
| `HtmlToPdf` | `.Options`, `.Header`, `.Footer`, `.ConversionResult`; `ConvertUrl(string)`, `ConvertHtmlString(string)`, `ConvertHtmlString(string, baseUrl)` → `PdfDocument`. |
| `HtmlToImage` | `ConvertUrl(string)`, `ConvertHtmlString(...)` → `System.Drawing.Image`; `.RenderingEngine`, `.WebPageWidth`, `.WebPageHeight`. |
| `HtmlToPdfResult` (`.ConversionResult`) | `.WebPageInformation` → `.Title`, `.Description`, `.Keywords`. |

**`HtmlToPdfOptions`** (via `converter.Options`): `RenderingEngine`, `PdfPageSize`, `PdfPageOrientation`, `PdfPageCustomSize`, `MarginLeft/Right/Top/Bottom`, `WebPageWidth`, `WebPageHeight`, `WebPageFixedSize`, `AutoFitWidth`, `AutoFitHeight`, `MinPageLoadTime`, `MaxPageLoadTime`, `MaxConversionTime`, `CssMediaType`, `InternalLinksEnabled`, `ExternalLinksEnabled`, `JavaScriptEnabled`, `PluginsEnabled`, `DrawBackground`, `EmbedFonts`, `KeepTextsTogether`, `KeepImagesTogether`, `RenderPageOnTimeout`, `GenerateSinglePagePdf`, `DenyLocalFileAccess`, `ColorSpace`, `PdfStandard`, `Tagged`, `Title`, `Language`, `PdfCompressionLevel`, `JpegCompressionEnabled`, `JpegCompressionLevel`, `MaximumConcurrentConversions`, `ManagedContentTrimming`, `ManagedContentTrimmingMode`, `WebGlEnabled`, `VisibleWebElementId`, `DisplayHeader`, `DisplayFooter`, `PdfBookmarkOptions`, `WebElementsMappingOptions`, `ViewerPreferences`, `SecurityOptions`, `Authentication`, `HttpHeaders`, `HttpCookies`, `HttpPostParameters`, `ProxyOptions`, `PdfDocumentInformation`. Blink/Chromium extras: `PageRanges`, `WindowStatus`, `CustomCSS`, `PostLoadingScript`, `LazyImagesLoadingEnabled`, `LazyImagesLoadingDelay`, `EnableOfflineMode`, `ClickElementsSelectors`, `VisibleWebElementSelector`, `BlinkEnginePath`, `CefEnginePath`, `LFN`.

→ `html-to-pdf.md`, `html-to-image.md`, `web-elements-and-auth.md`

## Headers / footers / page numbers

`converter.Header` / `converter.Footer` (`PdfHeader` / `PdfFooter`): `.Height`, `.Add(...)`, `DisplayOnFirstPage`, `DisplayOnOddPages`, `DisplayOnEvenPages`, `FirstPageNumber`, `TotalPagesOffset`. Section elements: `PdfHtmlSection` (`(url)` or `(html, baseUrl)`, `.AutoFitHeight`, `.RenderingEngine`), `PdfTextSection` (`(x, y, text, Font)`, `.Text`, `.HorizontalAlign`), `PdfImageSection`. Placeholders in text: `{page_number}`, `{total_pages}`.

→ `page-setup-headers-footers.md`

## PDF object model (build from scratch)

| Type | Key members |
|---|---|
| `PdfDocument` | `new PdfDocument()`, `new PdfDocument(string\|Stream)` (open existing), `AddPage()`, `Pages`, `Append(PdfDocument)`, `AddFont(...)`, `AddTemplate(...)`, `DocumentInformation`, `Security`, `ViewerPreferences`, `Bookmarks`, `OpenAction`, `AddZugferdInvoice(xml, ZugferdProfile, PdfAttachmentRelationship[, ZugferdSchema])`, `AddXmpExtensionSchema(prefix, namespaceUri, schemaName, properties)`, `Save()`/`Save(string)`, `Close()`. |
| `PdfPage` | `Add(element)` → `PdfRenderingResult`; `ClientRectangle`. |
| `PdfRenderingResult` | `PdfPageLastRectangle` (RectangleF). |
| `PdfTextElement` | `(x, y, text, PdfFont)`, `ForeColor`, `BackColor`, `HorizontalAlign`, `VerticalAlign`. |
| `PdfImageElement` | `(x, y, fileName)` / `(x, y, w, h, Image)`, `Transparency`. |
| `PdfHtmlElement` | `(htmlUrl)` / `(html, baseUrl)`, `.RenderingEngine`. |
| `PdfLineElement`, `PdfRectangleElement`, `PdfCircleElement`, `PdfEllipseElement`, `PdfBezierCurveElement` | `ForeColor`, `LineStyle`, `Gradient`. |
| `PdfTemplate` | `.Add(...)`, `.Background`, `.Foreground`. |
| `PdfDocumentInformation` | `Title`, `Author`, `Subject`, `Keywords`, `CreationDate`. |

→ `pdf-creation-elements.md`

## Edit existing PDFs

`PdfDocument(string\|Stream)` + `Append(...)`; `PdfResizeManager` (`Load`/`Save`/`Close`, `PageSize`, `PageOrientation`, `PageMargins`, `KeepAspectRatio`, …); `PdfPortfolioManager` / `PdfPortfolioFile`; stamps/watermarks via `PdfTemplate` + elements.

→ `edit-merge-split-stamp.md`

## Security & signatures

`HtmlToPdfOptions.SecurityOptions` / `PdfDocument.Security` (`PdfSecurityOptions`): `UserPassword`, `OwnerPassword`, `CanPrint`, `CanCopyContent`, `CanEditContent`, `CanEditAnnotations`, `CanFillFormFields`, `CanAssembleDocument`. `PdfSecurityManager`: `EncryptionAlgorithm` (`PdfEncryptionAlgorithm`), `EncryptionKeySize` (`PdfEncryptionKeySize`), `Sign(...)`. Signatures: `PdfDigitalSignatureElement` (`(RectangleF, PdfDigitalCertificate)`, `Reason`, `Location`, `ContactInfo`), `PdfDigitalCertificatesStore.GetCertificates`, `TimeStampAuthority`. Viewer: `PdfViewerPreferences` (`HideToolbar`, `FitWindow`, `PageMode`, `PageLayout`, …).

→ `security-and-signatures.md`

## Forms & compression (Select.Pdf.Extras)

`SelectPdf.Forms.PdfFormManager` (`Load`, `Fields`, `Save`, `Close`, `Flatten`, `ReadOnly`); field types `PdfFormFieldTextBox`, `PdfFormFieldCheckBox`, `PdfFormFieldComboBox`, `PdfFormFieldListBox`, `PdfFormFieldRadioButtonList`. `SelectPdf.Compressor.PdfCompressor` (`Load`, `Save`, image/stream compression options).

→ `forms-and-extras.md`

## Extraction

`PdfToText` (`Load`, `GetText()`, `GetHtml()`, `Search(text, caseSensitive, wholeWords)` → `TextPosition[]`, `Layout`, `StartPageNumber`, `EndPageNumber`). `PdfRasterizer` (`Load`, `ConvertToImages()` → `Image[]`, `ConvertToTiff()`, `Resolution`, `ColorSpace`).

→ `extract-text-image-search.md`

## Key enums

- `RenderingEngine`: `WebKit`, `WebKitRestricted`, `Blink`, `Chromium`
- `PdfPageOrientation`: `Portrait`, `Landscape`
- `HtmlToPdfPageFitMode`: `NoAdjustment`, `ShrinkOnly`, `AutoFit`
- `HtmlToPdfCssMediaType`: `Screen`, `Print`
- `PdfColorSpace`: `RGB`, `CMYK`, `GrayScale`
- `PdfStandard`: `Full`, `PdfA1B`, `PdfA2B`, `PdfA3B`, `PdfA3U`, `PdfA3A`, `PdfX1A`, … (use the value present in the source)
- `ZugferdProfile`: `Minimum`, `BasicWL`, `Basic`, `En16931`, `Extended`, `XRechnung`. `ZugferdSchema`: `FacturX10` (default), `Zugferd20`.
- `PdfAttachmentRelationship`: `Source`, `Data`, `Alternative`, `Supplement`, `Unspecified`.
- `ManagedContentTrimmingMode`: `Ink` (default), `Marker`.
- `PdfTextHorizontalAlign`: e.g. `Right`
- `PdfViewerPageMode`, `PdfViewerPageLayout`, `NetworkProxyType`, `PdfEncryptionAlgorithm`, `PdfEncryptionKeySize`

See also: html-to-pdf.md, pdf-creation-elements.md, edit-merge-split-stamp.md, security-and-signatures.md
