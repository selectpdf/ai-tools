# Extracting from PDFs — text, images, search

Pull text out of a PDF, render its pages to images, and locate text occurrences with the Select.Pdf .NET library's `PdfToText` and `PdfRasterizer` classes.

## Extracting text from a PDF

The PDF-to-text converter is the `PdfToText` class. Load the source PDF with the `Load` method, then call `GetText()` to get the extracted text as a plain `string` (or `GetHtml()` to get the same text wrapped in basic HTML). Restrict the operation to a page range with the `StartPageNumber` (default 1) and `EndPageNumber` (default 0 = to the end) properties, and control the output shape with the `Layout` property of type `TextLayout` (`TextLayout.Original` preserves column positions, `TextLayout.Reading` produces reading order).

```csharp
// instantiate a pdf to text converter object
PdfToText pdfToText = new PdfToText();

// load PDF file
pdfToText.Load(filePdf);

// set the properties
pdfToText.Layout = TextLayout.Original;
pdfToText.StartPageNumber = 1;
pdfToText.EndPageNumber = 0;

// extract the text
string text = pdfToText.GetText();
```

```vbnet
' instantiate a pdf to text converter object
Dim pdfToText As New PdfToText()

' load PDF file
pdfToText.Load(filePdf)

' set the properties
pdfToText.Layout = TextLayout.Original
pdfToText.StartPageNumber = 1
pdfToText.EndPageNumber = 0

' extract the text
Dim text As String = pdfToText.GetText()
```

## Rendering PDF pages to images

The PDF-to-image converter is the `PdfRasterizer` class. After `Load`, call `ConvertToImages()` to get a `System.Drawing.Image[]` with one image per page, or `ConvertToTiff()` to get a single multi-frame TIFF as a `byte[]`. Set `Resolution` (DPI, default 120) and `ColorSpace` (`PdfRasterizerColorSpace.RGB` default, or `.Gray`); the same `StartPageNumber`/`EndPageNumber` page-range properties apply.

```csharp
// instantiate a pdf rasterizer (pdf to image converter) object
PdfRasterizer rasterizer = new PdfRasterizer();

// load PDF file
rasterizer.Load(filePdf);

// set the properties
rasterizer.StartPageNumber = 1;
rasterizer.EndPageNumber = 0;
rasterizer.Resolution = 150;
rasterizer.ColorSpace = PdfRasterizerColorSpace.RGB;

// convert pages to images (one per page)
System.Drawing.Image[] images = rasterizer.ConvertToImages();

// or convert all selected pages to a single multi-frame TIFF
byte[] tiffBytes = rasterizer.ConvertToTiff();
```

```vbnet
' instantiate a pdf rasterizer (pdf to image converter) object
Dim rasterizer As New PdfRasterizer()

' load PDF file
rasterizer.Load(filePdf)

' set the properties
rasterizer.StartPageNumber = 1
rasterizer.EndPageNumber = 0
rasterizer.Resolution = 150
rasterizer.ColorSpace = PdfRasterizerColorSpace.RGB

' convert pages to images (one per page)
Dim images As System.Drawing.Image() = rasterizer.ConvertToImages()

' or convert all selected pages to a single multi-frame TIFF
Dim tiffBytes As Byte() = rasterizer.ConvertToTiff()
```

## Searching text in a PDF

`PdfToText` also searches: after `Load`, call `Search(text, caseSensitive, wholeWordsOnly)`, which returns a `TextPosition[]` — one entry per occurrence. Each `TextPosition` exposes `PageNumber` plus `X`, `Y`, `Width`, and `Height`, giving the location of the match (used below to draw highlight rectangles over the hits).

```csharp
// instantiate a pdf to text converter object
PdfToText pdfToText = new PdfToText();

// load PDF file
pdfToText.Load(filePdf);

// search for text and retrieve all found text positions
TextPosition[] positions = pdfToText.Search(searchText, caseSensitive, wholeWordsOnly);

foreach (TextPosition position in positions)
{
    int pageNumber = position.PageNumber;
    double x = position.X, y = position.Y;
    double w = position.Width, h = position.Height;
    // ... use the match location ...
}
```

```vbnet
' instantiate a pdf to text converter object
Dim pdfToText As New PdfToText()

' load PDF file
pdfToText.Load(filePdf)

' search for text and retrieve all found text positions
Dim positions As TextPosition() = pdfToText.Search(searchText, caseSensitive, wholeWordsOnly)

For Each position As TextPosition In positions
    Dim pageNumber As Integer = position.PageNumber
    Dim x As Double = position.X, y As Double = position.Y
    Dim w As Double = position.Width, h As Double = position.Height
    ' ... use the match location ...
Next
```

See also: edit-merge-split-stamp.md, html-to-image.md, api-cheatsheet.md
