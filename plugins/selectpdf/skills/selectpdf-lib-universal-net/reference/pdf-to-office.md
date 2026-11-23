# PDF to Word, Excel and PowerPoint

Converted from the shipped SelectPdf.Universal v26.6 help topics. C# and VB.NET samples are the official ones.

## PDF to Word, Excel and PowerPoint

SelectPdf Library for .NET converts a PDF document to an editable Office file with three converters: `PdfToWord` (Word, and also RTF, OpenDocument text, HTML or Markdown), `PdfToExcel` (an Excel workbook built from the document's tables) and `PdfToPowerPoint` (a PowerPoint presentation with a slide per page). The conversions run in-process.

Each converter has an `Options` property and three methods that return the converted file as an array of bytes: `ConvertFile` (a path), `ConvertStream` (a readable stream, read from its current position) and `ConvertBytes` (the PDF in memory). The options of every converter have a `PageRanges` property ("1", "2-4", "1-3,5,7-"; empty converts every page) and a `Password` property for an encrypted PDF.

### Pdf to Word

`PdfToWord` groups the text of every page into paragraphs in reading order, with their headings, lists, tables, pictures, hyperlinks and bookmarks. Its options (`PdfToWordOptions`):

- `PdfToWordOptions.Layout` - `PdfToWordLayout.Flowing` (the default) gives a document to edit, its paragraphs flowing as in any Word document; `PdfToWordLayout.Exact` reproduces every page as it is, each block of text in a frame at its place.
- `PdfToWordOptions.Format` - `Docx` (the default), `Rtf`, `Odt`, `Html` (a web page with its pictures inside) or `Markdown` (tables as pipe tables).
- `PdfToWordOptions.EmbedFonts` - embeds the document's fonts, so it looks like the PDF on a computer that lacks them (default true). A font whose license forbids embedding, or a common system font, is named only.
- `PdfToWordOptions.HeadersAndFooters` - text repeated at the top and bottom of the pages becomes the document's headers and footers, page numbers as page fields (default true; false leaves that text out).
- `PdfToWordOptions.Links`, `PdfToWordOptions.Bookmarks` and `PdfToWordOptions.Images` - keep the PDF's links, outline and pictures (all default true).

After a conversion, `PdfToWord.ConversionResult` lists in `PdfToWordResult.Fonts` every face of the document's text and whether it was embedded, written in a family with the same metrics, or named only.

```csharp
PdfToWord converter = new PdfToWord();
converter.Options.Layout = PdfToWordLayout.Flowing;
converter.Options.Format = PdfToWordFormat.Docx;
converter.Options.PageRanges = "1-5";

byte[] docx = converter.ConvertFile("report.pdf");
File.WriteAllBytes("report.docx", docx);

// what became of each font
foreach (PdfToWordFont font in converter.ConversionResult.Fonts)
    Console.WriteLine(font);
```

```vbnet
Dim converter As New PdfToWord()
converter.Options.Layout = PdfToWordLayout.Flowing
converter.Options.Format = PdfToWordFormat.Docx
converter.Options.PageRanges = "1-5"

Dim docx As Byte() = converter.ConvertFile("report.pdf")
File.WriteAllBytes("report.docx", docx)

' what became of each font
For Each font As PdfToWordFont In converter.ConversionResult.Fonts
    Console.WriteLine(font)
Next
```

### Pdf to Excel

`PdfToExcel` finds the tables of the document and writes them as cells: numbers, percentages, currency amounts and dates as typed values, text as text, header rows bold and frozen, merged cells merged. Its options (`PdfToExcelOptions`):

- `PdfToExcelOptions.Sheets` - `PdfToExcelSheets.PerTable` (the default; a document with no table gets a worksheet per page) or `PdfToExcelSheets.PerPage`.
- `PdfToExcelOptions.Culture` - the culture values are read in ("en-US", "de-DE"); empty takes the PDF's language, else the formats the values themselves show.
- `PdfToExcelOptions.TextOutsideTables` - with a worksheet per table, the text outside the tables goes on a worksheet of its own (default true).
- `PdfToExcelOptions.Images` - the document's pictures, on rows of their own (default true).

```csharp
PdfToExcel converter = new PdfToExcel();
converter.Options.Sheets = PdfToExcelSheets.PerTable;
converter.Options.Culture = "de-DE";
converter.Options.TextOutsideTables = false;

using (FileStream pdf = File.OpenRead("statement.pdf"))
{
    byte[] xlsx = converter.ConvertStream(pdf);
    File.WriteAllBytes("statement.xlsx", xlsx);
}
```

```vbnet
Dim converter As New PdfToExcel()
converter.Options.Sheets = PdfToExcelSheets.PerTable
converter.Options.Culture = "de-DE"
converter.Options.TextOutsideTables = False

Using pdf As FileStream = File.OpenRead("statement.pdf")
    Dim xlsx As Byte() = converter.ConvertStream(pdf)
    File.WriteAllBytes("statement.xlsx", xlsx)
End Using
```

### Pdf to PowerPoint

`PdfToPowerPoint` makes a slide of every page: each block of text a text box at its place, the page colour the slide's background, boxes and rules shapes, images and vector art pictures. The slides take the first page's size. Its options (`PdfToPowerPointOptions`) include `PdfToPowerPointOptions.Tables` (default true: the PDF's tables become PowerPoint tables; false keeps their text in text boxes and their lines as shapes), `Images`, `HeadersAndFooters` and `Links`.

```csharp
PdfToPowerPoint converter = new PdfToPowerPoint();
converter.Options.Tables = true;
converter.Options.PageRanges = "2-";

byte[] pdf = File.ReadAllBytes("slides.pdf");
byte[] pptx = converter.ConvertBytes(pdf);
File.WriteAllBytes("slides.pptx", pptx);
```

```vbnet
Dim converter As New PdfToPowerPoint()
converter.Options.Tables = True
converter.Options.PageRanges = "2-"

Dim pdf As Byte() = File.ReadAllBytes("slides.pdf")
Dim pptx As Byte() = converter.ConvertBytes(pdf)
File.WriteAllBytes("slides.pptx", pptx)
```

### Errors and Evaluation Mode

A file that does not exist, a PDF that cannot be read (or a wrong password) and a conversion that fails throw `PdfToWordException`, `PdfToExcelException` or `PdfToPowerPointException`. Without a license key the converted file carries an evaluation mark; see "License key" (installation-and-deployment.md).
