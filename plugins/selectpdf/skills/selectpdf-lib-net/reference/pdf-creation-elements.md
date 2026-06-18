# Building PDFs from scratch (object model)

Build a PDF imperatively with the commercial Select.Pdf library: create a `new PdfDocument`, add a page with `AddPage()` (or via the `Pages` collection), construct an element (`PdfTextElement`, `PdfImageElement`, `PdfHtmlElement`, graphic elements), call `page.Add(element)` to draw it, then `doc.Save(...)` and `doc.Close()`.

## Creating a document and adding pages

A document is a `PdfDocument`; every element must be drawn onto a `PdfPage`, which you obtain from `doc.AddPage()`. `page.Add(PdfPageElement)` returns a `PdfRenderingResult` whose `PdfPageLastRectangle` (a `RectangleF` with `Top`/`Bottom`/`Left`/`Right`) tells you where the element ended, so you can position the next element relative to it. Save with `doc.Save()` (returns a `byte[]`) or `doc.Save(file)` (file path), and always finish with `doc.Close()` to release native resources.

```csharp
using SelectPdf;

// create a new pdf document
PdfDocument doc = new PdfDocument();

// add a new page to the document
PdfPage page = doc.AddPage();

// add a font and an element
PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 20;

PdfRenderingResult result;
PdfTextElement text = new PdfTextElement(0, 0, "Hello world!", font);
result = page.Add(text);

// add 10 more lines, leaving 30pt between them
for (int i = 1; i <= 10; i++)
{
    PdfTextElement elem = new PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 30, "Text line " + i, font);
    result = page.Add(elem);
}

// save and close
doc.Save("output.pdf");
doc.Close();
```

```vbnet
Imports SelectPdf

' create a new pdf document
Dim doc As New PdfDocument()

' add a new page to the document
Dim page As PdfPage = doc.AddPage()

' add a font and an element
Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 20

Dim result As PdfRenderingResult
Dim text As New PdfTextElement(0, 0, "Hello world!", font)
result = page.Add(text)

' add 10 more lines, leaving 30pt between them
For i As Integer = 1 To 10
    Dim elem As New PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 30, "Text line " & i, font)
    result = page.Add(elem)
Next

' save and close
doc.Save("output.pdf")
doc.Close()
```

## PdfTextElement

`PdfTextElement` draws text at `X`/`Y`; an optional width constrains the destination rectangle (`Width`/`Height`). Fonts come from `doc.AddFont(...)` — either a built-in `PdfStandardFont` (e.g. `PdfStandardFont.Helvetica`) or a `System.Drawing.Font`; set the size via `PdfFont.Size`. Customize color with `ForeColor`/`BackColor`, alignment with `HorizontalAlign` (a `PdfTextHorizontalAlign`, e.g. `Right`) and `VerticalAlign`, and rotation with `Direction` (degrees, counter-clockwise). Constructors used here: `PdfTextElement(Single x, Single y, String text, PdfFont font)` and `PdfTextElement(Single x, Single y, Single width, String text, PdfFont font)`.

```csharp
using SelectPdf;

PdfDocument doc = new PdfDocument();

// standard (built-in) font
PdfFont font1 = doc.AddFont(PdfStandardFont.Helvetica);
font1.Size = 20;

// system font
PdfFont font2 = doc.AddFont(new System.Drawing.Font("Verdana", 15));

PdfPage page = doc.AddPage();
PdfRenderingResult result;

PdfTextElement text1 = new PdfTextElement(0, 0,
    "Regular text using standard font Helvetica, size 20.", font1);
result = page.Add(text1);

PdfTextElement text2 = new PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 20,
    "Regular text using system font Verdana, size 15.", font2);
result = page.Add(text2);

// colored text, width-limited to 200 points
PdfTextElement text3 = new PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 20, 200,
    "Red text over light blue background.", font2);
text3.ForeColor = System.Drawing.Color.Red;
text3.BackColor = System.Drawing.Color.LightBlue;
result = page.Add(text3);

// right-aligned colored text
PdfTextElement text4 = new PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 20, 300,
    "Red text over light green background, right aligned.", font2);
text4.ForeColor = System.Drawing.Color.Red;
text4.BackColor = System.Drawing.Color.LightGreen;
text4.HorizontalAlign = PdfTextHorizontalAlign.Right;
result = page.Add(text4);

// rotated text
PdfTextElement text5 = new PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 200, 300,
    "Text rotated 45 degrees.", font2);
text5.Direction = 45; // rotate 45 degrees counter-clockwise
result = page.Add(text5);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Imports SelectPdf

Dim doc As New PdfDocument()

' standard (built-in) font
Dim font1 As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font1.Size = 20

' system font
Dim font2 As PdfFont = doc.AddFont(New System.Drawing.Font("Verdana", 15))

Dim page As PdfPage = doc.AddPage()
Dim result As PdfRenderingResult

Dim text1 As New PdfTextElement(0, 0, _
    "Regular text using standard font Helvetica, size 20.", font1)
result = page.Add(text1)

Dim text2 As New PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 20, _
    "Regular text using system font Verdana, size 15.", font2)
result = page.Add(text2)

' colored text, width-limited to 200 points
Dim text3 As New PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 20, 200, _
    "Red text over light blue background.", font2)
text3.ForeColor = System.Drawing.Color.Red
text3.BackColor = System.Drawing.Color.LightBlue
result = page.Add(text3)

' right-aligned colored text
Dim text4 As New PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 20, 300, _
    "Red text over light green background, right aligned.", font2)
text4.ForeColor = System.Drawing.Color.Red
text4.BackColor = System.Drawing.Color.LightGreen
text4.HorizontalAlign = PdfTextHorizontalAlign.Right
result = page.Add(text4)

' rotated text
Dim text5 As New PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 200, 300, _
    "Text rotated 45 degrees.", font2)
text5.Direction = 45 ' rotate 45 degrees counter-clockwise
result = page.Add(text5)

doc.Save("output.pdf")
doc.Close()
```

## PdfImageElement

`PdfImageElement` places an image at `X`/`Y`. Construct it from a file path with `PdfImageElement(Single x, Single y, String fileName)` to keep the real image size, from a `System.Drawing.Image` with `PdfImageElement(Single x, Single y, Single width, Single height, Image image)` to force a size (aspect ratio not preserved), and set `Transparency` (0–100) to draw the image over existing content. As with all elements, `page.Add(img)` returns a `PdfRenderingResult` you can chain off (`PdfPageLastRectangle.Right`, `.Top`, etc.).

```csharp
using SelectPdf;

PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

string imgFile = @"C:\files\logo.png";
PdfRenderingResult result;

// image from file path, real size
PdfImageElement img1 = new PdfImageElement(0, 0, imgFile);
result = page.Add(img1);

// image from System.Drawing.Image, fixed 100x100 size
System.Drawing.Image img = System.Drawing.Image.FromFile(imgFile);
PdfImageElement img2 = new PdfImageElement(0, result.PdfPageLastRectangle.Bottom + 50, 100, 100, img);
result = page.Add(img2);

// image with 50% transparency, placed to the right of the previous one
PdfImageElement img3 = new PdfImageElement(
    result.PdfPageLastRectangle.Right + 30, result.PdfPageLastRectangle.Top, imgFile);
img3.Transparency = 50;
result = page.Add(img3);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Imports SelectPdf

Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

Dim imgFile As String = "C:\files\logo.png"
Dim result As PdfRenderingResult

' image from file path, real size
Dim img1 As New PdfImageElement(0, 0, imgFile)
result = page.Add(img1)

' image from System.Drawing.Image, fixed 100x100 size
Dim img As System.Drawing.Image = System.Drawing.Image.FromFile(imgFile)
Dim img2 As New PdfImageElement(0, result.PdfPageLastRectangle.Bottom + 50, 100, 100, img)
result = page.Add(img2)

' image with 50% transparency, placed to the right of the previous one
Dim img3 As New PdfImageElement( _
    result.PdfPageLastRectangle.Right + 30, result.PdfPageLastRectangle.Top, imgFile)
img3.Transparency = 50
result = page.Add(img3)

doc.Save("output.pdf")
doc.Close()
```

## PdfHtmlElement

`PdfHtmlElement` renders a web page or raw HTML and adds the result onto a page — use it when HTML is only a part of a larger object-model document (use `HtmlToPdf` instead when the whole PDF is an HTML conversion). Use `PdfHtmlElement(String urlOrFile)` to convert a full page by URL/local file, or `PdfHtmlElement(String url, String htmlString)` to convert a raw HTML string. Set the `RenderingEngine` property (a `RenderingEngine` enum: `WebKit`, `WebKitRestricted`, `Blink`, `Chromium`); as with every element, `page.Add(html)` returns a `PdfRenderingResult` with the layout / next-position info.

```csharp
using SelectPdf;

PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

// create html element from a URL
PdfHtmlElement html = new PdfHtmlElement("https://www.example.com");
html.RenderingEngine = RenderingEngine.Chromium;

// add the html element to the page
page.Add(html);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Imports SelectPdf

Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

' create html element from a URL
Dim html As New PdfHtmlElement("https://www.example.com")
html.RenderingEngine = RenderingEngine.Chromium

' add the html element to the page
page.Add(html)

doc.Save("output.pdf")
doc.Close()
```

## Graphic elements

Select.Pdf draws vector graphics with dedicated element types: `PdfLineElement(x1, y1, x2, y2)`, `PdfRectangleElement(x, y, width, height)`, `PdfCircleElement(x, y, radius)`, `PdfEllipseElement(x, y, width, height)`, and `PdfBezierCurveElement(x1, y1, x2, y2, x3, y3, x4, y4)`. Stroke color is `ForeColor`; line styling lives on `LineStyle` (`LineWidth`, `LineDashStyle` with `PdfLineDashStyle.Dot`/`Dash`, `LineCapStyle` with `PdfLineCapStyle.RoundCap`, `LineJoinStyle` with `PdfLineJoinStyle.RoundJoin`). Fill a shape with a gradient via the `Gradient` property set to `new PdfGradient(PdfGradientDirection.ForwardDiagonal, colorStart, colorEnd)`.

```csharp
using SelectPdf;

PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();
PdfRenderingResult result;

// solid horizontal line
PdfLineElement line1 = new PdfLineElement(0, 0, 400, 0);
line1.LineStyle.LineWidth = 3;
result = page.Add(line1);

// dotted red line
PdfLineElement line2 = new PdfLineElement(0, 30, 400, 30);
line2.ForeColor = System.Drawing.Color.Red;
line2.LineStyle.LineWidth = 2;
line2.LineStyle.LineDashStyle = PdfLineDashStyle.Dot;
result = page.Add(line2);

// dashed blue line with caps/joins
PdfLineElement line3 = new PdfLineElement(0, 60, 400, 60);
line3.ForeColor = System.Drawing.Color.Blue;
line3.LineStyle.LineWidth = 2;
line3.LineStyle.LineDashStyle = PdfLineDashStyle.Dash;
line3.LineStyle.LineCapStyle = PdfLineCapStyle.RoundCap;
line3.LineStyle.LineJoinStyle = PdfLineJoinStyle.RoundJoin;
result = page.Add(line3);

// rectangle
PdfRectangleElement rect1 = new PdfRectangleElement(0, 90, 400, 100);
result = page.Add(rect1);

// circle
PdfCircleElement circ1 = new PdfCircleElement(50, 250, 50);
circ1.ForeColor = System.Drawing.Color.Green;
result = page.Add(circ1);

// gradient-filled ellipse
PdfEllipseElement elli1 = new PdfEllipseElement(300, 250, 100, 50);
elli1.ForeColor = System.Drawing.Color.Brown;
elli1.Gradient = new PdfGradient(PdfGradientDirection.ForwardDiagonal,
    System.Drawing.Color.LightBlue, System.Drawing.Color.DarkBlue);
elli1.LineStyle.LineWidth = 2;
result = page.Add(elli1);

// bezier curve
PdfBezierCurveElement bez1 = new PdfBezierCurveElement(50, 350, 100, 400, 200, 350, 250, 370);
result = page.Add(bez1);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Imports SelectPdf

Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()
Dim result As PdfRenderingResult

' solid horizontal line
Dim line1 As New PdfLineElement(0, 0, 400, 0)
line1.LineStyle.LineWidth = 3
result = page.Add(line1)

' dotted red line
Dim line2 As New PdfLineElement(0, 30, 400, 30)
line2.ForeColor = System.Drawing.Color.Red
line2.LineStyle.LineWidth = 2
line2.LineStyle.LineDashStyle = PdfLineDashStyle.Dot
result = page.Add(line2)

' dashed blue line with caps/joins
Dim line3 As New PdfLineElement(0, 60, 400, 60)
line3.ForeColor = System.Drawing.Color.Blue
line3.LineStyle.LineWidth = 2
line3.LineStyle.LineDashStyle = PdfLineDashStyle.Dash
line3.LineStyle.LineCapStyle = PdfLineCapStyle.RoundCap
line3.LineStyle.LineJoinStyle = PdfLineJoinStyle.RoundJoin
result = page.Add(line3)

' rectangle
Dim rect1 As New PdfRectangleElement(0, 90, 400, 100)
result = page.Add(rect1)

' circle
Dim circ1 As New PdfCircleElement(50, 250, 50)
circ1.ForeColor = System.Drawing.Color.Green
result = page.Add(circ1)

' gradient-filled ellipse
Dim elli1 As New PdfEllipseElement(300, 250, 100, 50)
elli1.ForeColor = System.Drawing.Color.Brown
elli1.Gradient = New PdfGradient(PdfGradientDirection.ForwardDiagonal, _
    System.Drawing.Color.LightBlue, System.Drawing.Color.DarkBlue)
elli1.LineStyle.LineWidth = 2
result = page.Add(elli1)

' bezier curve
Dim bez1 As New PdfBezierCurveElement(50, 350, 100, 400, 200, 350, 250, 370)
result = page.Add(bez1)

doc.Save("output.pdf")
doc.Close()
```

## PDF templates (repeating backgrounds / headers)

A `PdfTemplate` holds elements that repeat automatically on every page. Create one with `doc.AddTemplate(RectangleF)` or `doc.AddTemplate(Single x, Single y)`, then add elements to it with `template.Add(...)` (coordinates are relative to the template's own rectangle). Set `template.Background = true` to render it behind the page content (default is in front). Templates support `Foreground`, `Docking`, `Anchoring`, `DisplayOnOddPages`/`DisplayOnEvenPages`/`DisplayOnFirstPage`, `FirstPageNumber`, and `TotalPagesOffset`; a `PdfTextElement` inside a template can use the `{page_number}` and `{total_pages}` placeholders in its `Text`. `PdfDocument` also exposes special `Header` and `Footer` templates.

```csharp
using SelectPdf;
using System.Drawing;

PdfDocument doc = new PdfDocument();
PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 24;

PdfPage page = doc.AddPage();
PdfTextElement text = new PdfTextElement(0, 0, "Some long body text...", font);
page.Add(text);

string imgFile = @"C:\files\logo.png";

// template with an image, repeats on all pages (in front of content)
PdfTemplate template1 = doc.AddTemplate(new RectangleF(100, 0, 400, 150));
PdfImageElement img1 = new PdfImageElement(0, 0, imgFile);
template1.Add(img1);

// template rendered behind the page content
PdfTemplate template2 = doc.AddTemplate(new RectangleF(100, 200, 400, 150));
template2.Background = true;
PdfImageElement img2 = new PdfImageElement(0, 0, imgFile);
template2.Add(img2);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Imports SelectPdf
Imports System.Drawing

Dim doc As New PdfDocument()
Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 24

Dim page As PdfPage = doc.AddPage()
Dim text As New PdfTextElement(0, 0, "Some long body text...", font)
page.Add(text)

Dim imgFile As String = "C:\files\logo.png"

' template with an image, repeats on all pages (in front of content)
Dim template1 As PdfTemplate = doc.AddTemplate(New RectangleF(100, 0, 400, 150))
Dim img1 As New PdfImageElement(0, 0, imgFile)
template1.Add(img1)

' template rendered behind the page content
Dim template2 As PdfTemplate = doc.AddTemplate(New RectangleF(100, 200, 400, 150))
template2.Background = True
Dim img2 As New PdfImageElement(0, 0, imgFile)
template2.Add(img2)

doc.Save("output.pdf")
doc.Close()
```

## Document properties

Set the PDF's metadata through `doc.DocumentInformation` (a `PdfDocumentInformation`): `Title`, `Author`, `Subject`, `Keywords`, `CreationDate` (a `DateTime`), and `Language`. Assign these any time before `doc.Save(...)`.

```csharp
using SelectPdf;
using System;

PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 20;
PdfTextElement text = new PdfTextElement(50, 50, "Document Properties Sample", font);
page.Add(text);

// set the document properties
doc.DocumentInformation.Title = "SelectPdf for .NET Sample";
doc.DocumentInformation.Author = "SelectPdf Samples";
doc.DocumentInformation.Subject = "SelectPdf Library Samples";
doc.DocumentInformation.Keywords = "pdf library, sample code";
doc.DocumentInformation.CreationDate = DateTime.Now;
doc.DocumentInformation.Language = "en-US";

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Imports SelectPdf
Imports System

Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 20
Dim text As New PdfTextElement(50, 50, "Document Properties Sample", font)
page.Add(text)

' set the document properties
doc.DocumentInformation.Title = "SelectPdf for .NET Sample"
doc.DocumentInformation.Author = "SelectPdf Samples"
doc.DocumentInformation.Subject = "SelectPdf Library Samples"
doc.DocumentInformation.Keywords = "pdf library, sample code"
doc.DocumentInformation.CreationDate = DateTime.Now
doc.DocumentInformation.Language = "en-US"

doc.Save("output.pdf")
doc.Close()
```

See also: edit-merge-split-stamp.md, security-and-signatures.md, forms-and-extras.md, api-cheatsheet.md
