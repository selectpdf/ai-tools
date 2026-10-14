# Redaction

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## Redaction

SelectPdf PDF Library for .NET can redact existing PDF documents: the text, images, vector graphics, links and form fields inside the areas you mark are deleted from the document, and each area is painted over with a fill color. The content is removed, not hidden - it cannot be selected, copied, searched or recovered from the redacted file.

Redaction is done with `PdfRedactionManager`, which exposes the following methods:

- `PdfRedactionManager.Load(string)` - loads the existing PDF document, password protected or not, from a file, Stream, byte array or `PdfDocument` object.
- `PdfRedactionManager.AddRedaction(string, bool, bool)` - marks every occurrence of a text for redaction, on every page or on the pages you name. The text can be matched with or without letter case and as whole words only; a text of several words is found also when it continues on the next line, and then gets one area per line.
- `PdfRedactionManager.AddRedaction(int, RectangleF)` - marks a rectangle on a page for redaction, optionally with a fill color of its own.
- `PdfRedactionManager.Redact` - applies the marked areas and returns one `PdfRedactionResult` per area: the page, the rectangle that was redacted and, for an area found by text, the text.
- `PdfRedactionManager.Save` - saves the redacted document to a file, Stream or byte array. Areas marked and not yet applied are applied first, so nothing marked is saved unredacted.
- `PdfRedactionManager.GetDocument` - returns the redacted document as a `PdfDocument` object, to continue working on it.

The color that covers an area is set with `PdfRedactionManager.FillColor` (black by default), or per area.

### What is removed

- Text is removed glyph by glyph, and the text around it keeps its position exactly. A glyph is removed when the area covers its center, or a quarter or more of its width and of its height - so a letter the area cuts through goes with the rest, while a neighbor the area only touches at its edge stays.
- The part of an image inside the area is erased from the image data itself; an image that cannot be edited is removed whole. Images shared with other pages are changed only on the redacted page.
- Links, annotations and form fields that touch the area are removed.
- Vector graphics inside the area are removed; those crossing its edge are covered.

### Text found by search, everywhere it is stored

A text marked for redaction is found wherever a page shows it - in the page's own content, and in the appearance of an annotation or a form field, which is then removed with its area. A text marked on every page is also taken out of what the document stores without showing it, so that a search of the redacted file finds it nowhere:

- the document properties (title, author, subject, keywords) and the XMP metadata;
- bookmark titles;
- the text of annotations and form fields - comments, their authors, field values and tooltips;
- the alternate and actual texts of a tagged document;
- attachments: their names and descriptions, and their content. An attached PDF is redacted in turn; a text file, and the XML inside an Office or OpenDocument file, are edited; any other attachment that contains the text is removed from the document.

Each occurrence is deleted from the text it is in, with the same letter case and whole-word rules as the search on the pages. A text marked on some pages only changes those pages. Text that is part of an image, such as a scanned page, is not text: redact it by area.

### Patterns and overlay text

Text that follows a pattern rather than a known value - account numbers, social security numbers, e-mail addresses - is marked with a regular expression: `PdfRedactionManager.AddRedaction(Regex)`. Every match is removed exactly as a text would be, on the pages and, when no pages are named, from what the document stores off them. The expression's own options decide the letter case, and \b marks word boundaries. It runs over the text of each page as it reads, so a match can wrap onto the next line.

`PdfRedactionManager.OverlayText` writes a text such as "[REDACTED]" in every redacted area, centred and sized to fit, in `PdfRedactionManager.OverlayTextColor` (white by default, to show on the default black fill). It is written in an embedded font, so PDF/A and PDF/UA documents stay conformant, and it reads upright on a rotated page.

Text drawn at an angle - on a page turned for display, or set at any angle - is removed like any other text.

### Coordinates

Areas are given in points from the top-left corner of the visible page (its crop box), in the page's own orientation. On a rotated page the area turns with the page when it is displayed, as the content does. The rectangles returned by `PdfRedactionManager.Redact` use the same coordinates.

### Accessibility, PDF/A, encryption and signatures

- A tagged document keeps its logical structure and its PDF/UA declaration, and a PDF/A document keeps its conformance level: the fill boxes are marked as artifacts, and structure that referred to removed links is taken out.
- An encrypted document stays encrypted with the same passwords and permissions, whichever of its passwords it was loaded with.
- The redacted document is written out whole, never appended to, so the removed content is not kept in an earlier revision of the file. As a consequence a digital signature the document carried no longer verifies after the redaction.

### Code Sample

```csharp
// load the document to redact
PdfRedactionManager manager = new PdfRedactionManager();
manager.Load("contract.pdf");

// every occurrence of a name, in any letter case, as a whole word
manager.AddRedaction("John Smith", false, true);

// an account number, only on the first two pages
manager.AddRedaction("RO49AAAA1B31007593840000", true, true, new int[] { 0, 1 });

// a signature image on the last page, covered in gray
manager.AddRedaction(2,
    new SelectPdf.Universal.Drawing.RectangleF(50, 650, 200, 80),
    new PdfColor(128, 128, 128));

// every social security number, by pattern, and a label in each area
manager.AddRedaction(
    new System.Text.RegularExpressions.Regex(@"\b\d{3}-\d{2}-\d{4}\b"));
manager.OverlayText = "[REDACTED]";

// apply and report what was redacted
PdfRedactionResult[] results = manager.Redact();
foreach (PdfRedactionResult result in results)
{
    Console.WriteLine("Page {0}: {1} at {2}, {3}",
        result.PageIndex, result.Text ?? "area", result.Bounds.X, result.Bounds.Y);
}

// save the redacted document
manager.Save("contract-redacted.pdf");
manager.Close();
```

```vbnet
' load the document to redact
Dim manager As New PdfRedactionManager()
manager.Load("contract.pdf")

' every occurrence of a name, in any letter case, as a whole word
manager.AddRedaction("John Smith", False, True)

' an account number, only on the first two pages
manager.AddRedaction("RO49AAAA1B31007593840000", True, True, New Integer() {0, 1})

' a signature image on the last page, covered in gray
manager.AddRedaction(2,
    New SelectPdf.Universal.Drawing.RectangleF(50, 650, 200, 80),
    New PdfColor(128, 128, 128))

' every social security number, by pattern, and a label in each area
manager.AddRedaction(
    New System.Text.RegularExpressions.Regex("\b\d{3}-\d{2}-\d{4}\b"))
manager.OverlayText = "[REDACTED]"

' apply and report what was redacted
Dim results As PdfRedactionResult() = manager.Redact()
For Each result As PdfRedactionResult In results
    Console.WriteLine("Page {0}: {1} at {2}, {3}",
        result.PageIndex, If(result.Text, "area"), result.Bounds.X, result.Bounds.Y)
Next

' save the redacted document
manager.Save("contract-redacted.pdf")
manager.Close()
```
