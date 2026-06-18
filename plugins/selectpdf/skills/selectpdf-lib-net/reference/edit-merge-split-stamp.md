# Editing existing PDFs — load, merge, split, stamp, resize

How to load, modify, merge, split, stamp, watermark, resize, add headers/footers to, and package existing PDF documents with the commercial Select.Pdf .NET library.

## Loading and modifying an existing PDF

Pass a file path (or a stream) to the `PdfDocument` constructor to open an existing document; the same `PdfDocument` constructors also accept a `Stream`. Once loaded, every authoring operation applies: add pages with `AddPage()`, add fonts with `AddFont(PdfStandardFont)`, add elements such as `PdfTextElement` to a page via `page.Add(...)`, and add templates. Produce the result with `doc.Save()` (returns a `byte[]`) and release resources with `doc.Close()`.

```csharp
// load an existing pdf document from disk
PdfDocument doc = new PdfDocument("doc1.pdf");

// add a new page to the document
PdfPage page = doc.AddPage();

// create a standard font
PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 20;

// create a text element and add it to the new page
PdfTextElement text = new PdfTextElement(100, 100,
    "Sample text added to an existing pdf document.", font);
page.Add(text);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
' load an existing pdf document from disk
Dim doc As New PdfDocument("doc1.pdf")

' add a new page to the document
Dim page As PdfPage = doc.AddPage()

' create a standard font
Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 20

' create a text element and add it to the new page
Dim text As New PdfTextElement(100, 100, _
    "Sample text added to an existing pdf document.", font)
page.Add(text)

doc.Save("output.pdf")
doc.Close()
```

## Merging PDFs

Load each source document into its own `PdfDocument`, create an empty destination `PdfDocument`, and append the pages of each source with `PdfDocument.Append(PdfDocument)`. After saving, close the destination and every source document.

```csharp
// load the source documents
PdfDocument doc1 = new PdfDocument("doc1.pdf");
PdfDocument doc2 = new PdfDocument("doc2.pdf");

// create the destination document and append the sources
PdfDocument doc = new PdfDocument();
doc.Append(doc1);
doc.Append(doc2);

doc.Save("output.pdf");

// close all documents
doc.Close();
doc1.Close();
doc2.Close();
```

```vbnet
' load the source documents
Dim doc1 As New PdfDocument("doc1.pdf")
Dim doc2 As New PdfDocument("doc2.pdf")

' create the destination document and append the sources
Dim doc As New PdfDocument()
doc.Append(doc1)
doc.Append(doc2)

doc.Save("output.pdf")

' close all documents
doc.Close()
doc1.Close()
doc2.Close()
```

## Splitting a PDF

Load the source `PdfDocument` and read its `Pages` collection (`Pages.Count`, indexed by `Pages[i]`). Create a new `PdfDocument` and copy the wanted pages into it with the `AddPage(PdfPage)` overload, which takes a page from the source collection. This extracts a page range into a new document.

```csharp
// load the source document
PdfDocument doc1 = new PdfDocument("doc.pdf");

// create a new document containing only pages 2 and 3
PdfDocument doc = new PdfDocument();
if (doc1.Pages.Count > 2)
{
    doc.AddPage(doc1.Pages[1]);
    doc.AddPage(doc1.Pages[2]);
}

doc.Save("output.pdf");
doc.Close();
```

```vbnet
' load the source document
Dim doc1 As New PdfDocument("doc.pdf")

' create a new document containing only pages 2 and 3
Dim doc As New PdfDocument()
If doc1.Pages.Count > 2 Then
    doc.AddPage(doc1.Pages(1))
    doc.AddPage(doc1.Pages(2))
End If

doc.Save("output.pdf")
doc.Close()
```

## Stamps

Stamps repeat content on every page using a PDF template. Create a template sized to the page with `doc.AddTemplate(doc.Pages[0].ClientRectangle)`, add a `PdfImageElement` (or other element) to it, and the template renders automatically on all pages. `PdfPage.ClientRectangle` exposes `Width` and `Height` for positioning.

```csharp
PdfDocument doc = new PdfDocument();
PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 24;
PdfPage page = doc.AddPage();
PdfTextElement text = new PdfTextElement(0, 0, "Some long text...", font);
page.Add(text);

// stamp all pages with an image in the bottom-right corner
PdfTemplate template = doc.AddTemplate(doc.Pages[0].ClientRectangle);
PdfImageElement img = new PdfImageElement(
    doc.Pages[0].ClientRectangle.Width - 300,
    doc.Pages[0].ClientRectangle.Height - 150, "logo.png");
template.Add(img);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 24
Dim page As PdfPage = doc.AddPage()
Dim text As New PdfTextElement(0, 0, "Some long text...", font)
page.Add(text)

' stamp all pages with an image in the bottom-right corner
Dim template As PdfTemplate = doc.AddTemplate(doc.Pages(0).ClientRectangle)
Dim img As New PdfImageElement( _
    doc.Pages(0).ClientRectangle.Width - 300, _
    doc.Pages(0).ClientRectangle.Height - 150, "logo.png")
template.Add(img)

doc.Save("output.pdf")
doc.Close()
```

## Watermarks

A watermark is a stamp rendered behind page content. Build the same template + element as a stamp, but set `PdfTemplate.Background = true` so it draws behind the rest of the page, and set `PdfImageElement.Transparency` (0–100) to make the image semi-transparent. A `PdfTextElement` can be used the same way for a text watermark.

```csharp
PdfDocument doc = new PdfDocument();
PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 24;
PdfPage page = doc.AddPage();
page.Add(new PdfTextElement(0, 0, "Some long text...", font));

// watermark all pages with a transparent background image
PdfTemplate template = doc.AddTemplate(doc.Pages[0].ClientRectangle);
PdfImageElement img = new PdfImageElement(
    doc.Pages[0].ClientRectangle.Width - 300,
    doc.Pages[0].ClientRectangle.Height - 150, "logo.png");
img.Transparency = 50;
template.Background = true;
template.Add(img);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 24
Dim page As PdfPage = doc.AddPage()
page.Add(New PdfTextElement(0, 0, "Some long text...", font))

' watermark all pages with a transparent background image
Dim template As PdfTemplate = doc.AddTemplate(doc.Pages(0).ClientRectangle)
Dim img As New PdfImageElement( _
    doc.Pages(0).ClientRectangle.Width - 300, _
    doc.Pages(0).ClientRectangle.Height - 150, "logo.png")
img.Transparency = 50
template.Background = True
template.Add(img)

doc.Save("output.pdf")
doc.Close()
```

## Resizing pages

`PdfResizeManager` changes page size, orientation, and margins of an existing document. Call `Load(...)` (file, `Stream`, byte array, or `PdfDocument`, password-protected or not), set properties, then `Save(...)` and `Close()`. Properties: `PageSize` (a `PdfCustomPageSize`, default A4), `PageOrientation` (`PdfPageOrientation`, default Portrait), `PageMargins` (a `PdfMargins`, added to existing margins), `AllowScale`, `KeepAspectRatio`, `HorizontalAlign` (`PdfHorizontalAlign`), `VerticalAlign` (`PdfVerticalAlign`), `StartPosition`, `ContentSize`, and `KeepOriginalPageSettings` (default false). `PdfMargins` takes `(left, right, top, bottom)`.

```csharp
PdfResizeManager resizer = new PdfResizeManager();
resizer.Load("selectpdf.pdf");

resizer.PageMargins = new PdfMargins(10, 10, 10, 10);
resizer.PageSize = PdfCustomPageSize.A4;
resizer.PageOrientation = PdfPageOrientation.Portrait;
resizer.AllowScale = true;
resizer.KeepAspectRatio = true;
resizer.HorizontalAlign = PdfHorizontalAlign.Center;
resizer.VerticalAlign = PdfVerticalAlign.Middle;

resizer.Save("output.pdf");
resizer.Close();
```

```vbnet
Dim resizer As New PdfResizeManager()
resizer.Load("selectpdf.pdf")

resizer.PageMargins = New PdfMargins(10, 10, 10, 10)
resizer.PageSize = PdfCustomPageSize.A4
resizer.PageOrientation = PdfPageOrientation.Portrait
resizer.AllowScale = True
resizer.KeepAspectRatio = True
resizer.HorizontalAlign = PdfHorizontalAlign.Center
resizer.VerticalAlign = PdfVerticalAlign.Middle

resizer.Save("output.pdf")
resizer.Close()
```

## Adding headers/footers to an existing PDF

The newly-created-document header/footer API does not apply to existing files. Instead, first resize with `PdfResizeManager` to create top/bottom space (extra margins via `PageMargins`), then call `PdfResizeManager.GetDocument()` to get the resized `PdfDocument` and add header/footer templates. Use `doc.AddTemplate(width, height)` for a header band, or `doc.AddTemplate(RectangleF)` to place a footer at a given rectangle. Header/footer text elements may use the placeholders `{page_number}` and `{total_pages}`, and `PdfTextElement.ForeColor` sets the color. Save through the `PdfDocument` and close via the resizer.

```csharp
PdfResizeManager resizer = new PdfResizeManager();
resizer.Load("selectpdf.pdf");

// add extra top and bottom margins (left, right, top, bottom)
resizer.PageMargins = new PdfMargins(0, 0, 90, 40);

PdfDocument doc = resizer.GetDocument();

// header template (90 points tall) with an image
PdfTemplate header = doc.AddTemplate(doc.Pages[0].ClientRectangle.Width, 90);
header.Add(new PdfImageElement(10, 10, "logo.png"));

// footer template (40 points tall) with text
PdfTemplate footer = doc.AddTemplate(new System.Drawing.RectangleF(0,
    doc.Pages[0].ClientRectangle.Height - 40,
    doc.Pages[0].ClientRectangle.Width, 40));

PdfFont font2 = doc.AddFont(PdfStandardFont.Helvetica);
font2.Size = 12;
PdfTextElement text1 = new PdfTextElement(10, 10,
    "Generated by SelectPdf. Page number {page_number} of {total_pages}.", font2);
text1.ForeColor = System.Drawing.Color.Blue;
footer.Add(text1);

doc.Save("output.pdf");
resizer.Close();
```

```vbnet
Dim resizer As New PdfResizeManager()
resizer.Load("selectpdf.pdf")

' add extra top and bottom margins (left, right, top, bottom)
resizer.PageMargins = New PdfMargins(0, 0, 90, 40)

Dim doc As PdfDocument = resizer.GetDocument()

' header template (90 points tall) with an image
Dim header As PdfTemplate = doc.AddTemplate(doc.Pages(0).ClientRectangle.Width, 90)
header.Add(New PdfImageElement(10, 10, "logo.png"))

' footer template (40 points tall) with text
Dim footer As PdfTemplate = doc.AddTemplate(New System.Drawing.RectangleF(0, _
    doc.Pages(0).ClientRectangle.Height - 40, _
    doc.Pages(0).ClientRectangle.Width, 40))

Dim font2 As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font2.Size = 12
Dim text1 As New PdfTextElement(10, 10, _
    "Generated by SelectPdf. Page number {page_number} of {total_pages}.", font2)
text1.ForeColor = System.Drawing.Color.Blue
footer.Add(text1)

doc.Save("output.pdf")
resizer.Close()
```

## PDF portfolio

A PDF Portfolio packages multiple files of any type into one integrated PDF; each file keeps its own identity. Use `PdfPortfolioManager`: create it (or call `Load(String)` to open/convert an existing PDF, which becomes the cover page), add files via the `Files` collection with `Files.Add(PdfPortfolioFile)` (and `Files.Remove(Int32)` by index), set `ViewMode` (`PdfPortfolioViewMode.Details` for a list view or `PdfPortfolioViewMode.Tile` for a thumbnail grid) and `StartupFileName`, then `Save(...)` and `Close()`. Each `PdfPortfolioFile` is constructed from a path and exposes `Description` (and, for reading embedded files, `Content` / `ContentAsStream`).

```csharp
PdfPortfolioManager portfolio = new PdfPortfolioManager();

PdfPortfolioFile pdfFilePdf = new PdfPortfolioFile("doc.pdf");
pdfFilePdf.Description = "PDF document";
portfolio.Files.Add(pdfFilePdf);

PdfPortfolioFile pdfFileHtml = new PdfPortfolioFile("document.html");
pdfFileHtml.Description = "HTML document";
portfolio.Files.Add(pdfFileHtml);

PdfPortfolioFile pdfFileImage = new PdfPortfolioFile("logo.png");
pdfFileImage.Description = "Image file";
portfolio.Files.Add(pdfFileImage);

portfolio.ViewMode = PdfPortfolioViewMode.Details;
portfolio.StartupFileName = "logo.png";

portfolio.Save("output.pdf");
portfolio.Close();
```

```vbnet
Dim portfolio As New PdfPortfolioManager()

Dim pdfFilePdf As New PdfPortfolioFile("doc.pdf")
pdfFilePdf.Description = "PDF document"
portfolio.Files.Add(pdfFilePdf)

Dim pdfFileHtml As New PdfPortfolioFile("document.html")
pdfFileHtml.Description = "HTML document"
portfolio.Files.Add(pdfFileHtml)

Dim pdfFileImage As New PdfPortfolioFile("logo.png")
pdfFileImage.Description = "Image file"
portfolio.Files.Add(pdfFileImage)

portfolio.ViewMode = PdfPortfolioViewMode.Details
portfolio.StartupFileName = "logo.png"

portfolio.Save("output.pdf")
portfolio.Close()
```

See also: pdf-creation-elements.md, security-and-signatures.md, api-cheatsheet.md
