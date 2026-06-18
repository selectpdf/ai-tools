# Headers, footers and page numbering (Community Edition)

How to add document headers and footers — including HTML/text sections and `{page_number}` / `{total_pages}` page numbering — to PDFs produced by the free Community Edition (`Select.HtmlToPdf`).

## Enabling the header and footer

A header or footer is only rendered when it is explicitly enabled on the converter options. Set `HtmlToPdfOptions.DisplayHeader` (via `converter.Options.DisplayHeader`) to `true` to show the header, and `HtmlToPdfOptions.DisplayFooter` to `true` to show the footer; while either is `false`, every option set on the corresponding header/footer object has no effect. The header is configured through the `converter.Header` property (a `PdfHeader`) and the footer through `converter.Footer` (a `PdfFooter`). Both objects expose `Height` (the band height) plus `DisplayOnFirstPage`, `DisplayOnOddPages`, `DisplayOnEvenPages`, `FirstPageNumber`, and `TotalPagesOffset`.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// header settings
converter.Options.DisplayHeader = true;
converter.Header.DisplayOnFirstPage = true;
converter.Header.DisplayOnOddPages = true;
converter.Header.DisplayOnEvenPages = true;
converter.Header.Height = 50;

// footer settings
converter.Options.DisplayFooter = true;
converter.Footer.DisplayOnFirstPage = true;
converter.Footer.DisplayOnOddPages = true;
converter.Footer.DisplayOnEvenPages = true;
converter.Footer.Height = 50;
```

```vbnet
Dim converter As New HtmlToPdf()

' header settings
converter.Options.DisplayHeader = True
converter.Header.DisplayOnFirstPage = True
converter.Header.DisplayOnOddPages = True
converter.Header.DisplayOnEvenPages = True
converter.Header.Height = 50

' footer settings
converter.Options.DisplayFooter = True
converter.Footer.DisplayOnFirstPage = True
converter.Footer.DisplayOnOddPages = True
converter.Footer.DisplayOnEvenPages = True
converter.Footer.Height = 50
```

## Adding HTML and text sections

Content is placed into a header or footer by calling its `Add(...)` method with a section element. The CE sources show two section types: `PdfHtmlSection` (HTML content, constructed from a URL or from an HTML string plus base URL) and `PdfTextSection` (plain text). For HTML sections, set `PdfHtmlSection.AutoFitHeight` to a `HtmlToPdfPageFitMode` value (e.g. `HtmlToPdfPageFitMode.AutoFit`) so the band sizes to its content. The example below adds HTML to the header and HTML to the footer.

```csharp
// add some html content to the header
PdfHtmlSection headerHtml = new PdfHtmlSection(headerUrl);
headerHtml.AutoFitHeight = HtmlToPdfPageFitMode.AutoFit;
converter.Header.Add(headerHtml);

// add some html content to the footer
PdfHtmlSection footerHtml = new PdfHtmlSection(footerUrl);
footerHtml.AutoFitHeight = HtmlToPdfPageFitMode.AutoFit;
converter.Footer.Add(footerHtml);
```

```vbnet
' add some html content to the header
Dim headerHtml As New PdfHtmlSection(headerUrl)
headerHtml.AutoFitHeight = HtmlToPdfPageFitMode.AutoFit
converter.Header.Add(headerHtml)

' add some html content to the footer
Dim footerHtml As New PdfHtmlSection(footerUrl)
footerHtml.AutoFitHeight = HtmlToPdfPageFitMode.AutoFit
converter.Footer.Add(footerHtml)
```

## Page numbering

Page numbers are produced with a `PdfTextSection` whose `Text` contains the `{page_number}` and `{total_pages}` placeholders; the converter substitutes the current page index and total page count at render time. By default page numbers start at 1 — change the start with `FirstPageNumber`, and shift the reported total with `TotalPagesOffset` (useful when the output will be merged with other documents). The `PdfTextSection(x, y, text, font)` constructor takes the position, the text, and a `System.Drawing.Font`; use `HorizontalAlign` (a `PdfTextHorizontalAlign` value such as `PdfTextHorizontalAlign.Right`) to align the text within the band. Add the section to `converter.Footer` (or `converter.Header`) with `Add(...)`.

```csharp
// page numbers can be added using a PdfTextSection object
PdfTextSection text = new PdfTextSection(0, 10, "Page: {page_number} of {total_pages}  ", new System.Drawing.Font("Arial", 8));
text.HorizontalAlign = PdfTextHorizontalAlign.Right;
converter.Footer.Add(text);
```

```vbnet
' page numbers can be added using a PdfTextSection object
Dim text As New PdfTextSection(0, 10, "Page: {page_number} of {total_pages}  ", New System.Drawing.Font("Arial", 8))
text.HorizontalAlign = PdfTextHorizontalAlign.Right
converter.Footer.Add(text)
```

See also: html-to-pdf.md, installation-and-engines.md
