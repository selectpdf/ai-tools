# Creating PDF documents from scratch

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## Getting started

To start using SelectPdf Library, follow the instructions from the "Installation" (installation-and-deployment.md) section to set up a project and add the **SelectPdf.Universal** NuGet package (assembly **SelectPdf.Universal.dll**).

This section will show how to create a simple 'Hello World!' application with Select.Pdf.

### Import the namespace

To use Select.Pdf classes, a namespace needs to be imported.

```csharp
			using SelectPdf.Universal;
```

```vbnet
			Imports SelectPdf.Universal
```

### Create the document

The first thing to do when using Select.Pdf library is to create the pdf document that is an instance of `PdfDocument` class.

```csharp
// create a new pdf document
PdfDocument doc = new PdfDocument();
```

```vbnet
' create a new pdf document
Dim doc As New PdfDocument()
```

### Create the first page of the document

Once the document is created, a first page is need in the pdf document. To do that, the method `PdfDocument.AddPage` of the `PdfDocument` class can be used. A page in a pdf document is an instance of the `PdfPage` class.

```csharp
// add a new page to the document
PdfPage page = doc.AddPage();
```

```vbnet
' add a new page to the document
Dim page As PdfPage = doc.AddPage()
```

### Create a font

To add a text to a pdf document, a font is needed. A font in a pdf document is an instance of the `PdfFont` class.

```csharp
// create a new pdf font
PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
font.Size = 20;
```

```vbnet
' create a new pdf font
Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
font.Size = 20
```

### Create a text element

To add a text to a pdf document, the `PdfTextElement` class can be used. After the `PdfTextElement` object is created, it must be added to the appropriate `PdfPage` object.

```csharp
// create a new text element and add it to the page
PdfTextElement text = new PdfTextElement(50, 50, "Hello world!", font);
page.Add(text);
```

```vbnet
' create a new text element and add it to the page
Dim text As New PdfTextElement(50, 50, "Hello world!", font)
page.Add(text)
```

### Save the document

Once the content is created, you can save the document. The document can be saved to a stream or file on disk.

```csharp
// save pdf document
doc.Save(file);

// close pdf document
doc.Close();
```

```vbnet
' save pdf document
doc.Save(file)

' close pdf document
doc.Close()
```

`PdfDocument` implements `IDisposable`, so a `using` statement closes it too. Calling `PdfDocument.Close` and disposing the document are the same; doing both is safe.

```csharp
using (PdfDocument doc = new PdfDocument())
{
    PdfPage page = doc.AddPage();
    // ... add content ...
    doc.Save(file);
}
```

```vbnet
Using doc As New PdfDocument()
    Dim page As PdfPage = doc.AddPage()
    ' ... add content ...
    doc.Save(file)
End Using
```

### Full Code

The full code of the 'Hello World!' application looks like this:

```csharp
// create a new pdf document
PdfDocument doc = new PdfDocument();

// add a new page to the document
PdfPage page = doc.AddPage();

// create a new pdf font
PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
font.Size = 20;

// create a new text element and add it to the page
PdfTextElement text = new PdfTextElement(50, 50, "Hello world!", font);
page.Add(text);

// save pdf document
doc.Save(file);

// close pdf document
doc.Close();
```

```vbnet
' create a new pdf document
Dim doc As New PdfDocument()

' add a new page to the document
Dim page As PdfPage = doc.AddPage()

' create a new pdf font
Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
font.Size = 20

' create a new text element and add it to the page
Dim text As New PdfTextElement(50, 50, "Hello world!", font)
page.Add(text)

' save pdf document
doc.Save(file)

' close pdf document
doc.Close()
```

## PDF elements

Select.Pdf supports a large number of element types that can be added to the pdf documents it manages:

- Text Element (`PdfTextElement`)
- Image Element (`PdfImageElement`)
- HTML Element (`PdfHtmlElement`)
- Graphic elements: Circle Element (`PdfCircleElement`), Ellipse Element (`PdfEllipseElement`), Ellipse Slice Element (`PdfEllipseSliceElement`), Ellipse Arc Element (`PdfEllipseArcElement`), Line Element (`PdfLineElement`), Polygon Element (`PdfPolygonElement`), Rectangle Element (`PdfRectangleElement`), Bezier Curve Element (`PdfBezierCurveElement`)
- Annotation Element (`PdfAnnotationElement`)
- Sound Link Element (`PdfSoundLinkElement`)
- File Attachment Element (`PdfFileAttachmentElement`)
- External File Element (`PdfExternalFileElement`)
- Internal Link Element (`PdfInternalLinkElement`)
- External Link Element (`PdfExternalLinkElement`)
- Digital Signature Element (`PdfDigitalSignatureElement`)

### Add Elements to Pdf

To add an element to a pdf document, at least one pdf page (instance of `PdfPage`) needs to be defined. To create a pdf page into a new document, the following code can be used:

```csharp
// create a new pdf document
PdfDocument doc = new PdfDocument();

// add a new page to the document
PdfPage page = doc.AddPage();
```

```vbnet
' create a new pdf document
Dim doc As New PdfDocument()

' add a new page to the document
Dim page As PdfPage = doc.AddPage()
```

Pdf elements are added to pdf pages using the `PdfPage.Add(PdfPageElement)` method of the `PdfPage` object. `PdfPage.Add(PdfPageElement)` returns a `PdfRenderingResult` object that contains information about the position where the pdf element was rendered.

The following code shows how to add a text element to a pdf page and how to retrieve the rendering results object:

```csharp
// define a rendering result object
PdfRenderingResult result;

// create a new text element and add it to the page
// Important: get the rendering result returned by Add() 
// into the PdfRenderingResult object
PdfTextElement text = new PdfTextElement(0, 0, "Hello world!", font);
result = page.Add(text);
```

```vbnet
' define a rendering result object
Dim result As PdfRenderingResult

' create a new text element and add it to the page
' Important: get the rendering result returned by Add() 
' into the PdfRenderingResult object
Dim text As New PdfTextElement(0, 0, "Hello world!", font)
result = page.Add(text)
```

### Code Sample

The following sample code shows how to create a new PDF document using Select.Pdf and add several text elements to it, for each additional element using the finish position of the previous element, to introduce some space between the lines of text.

```csharp
// create a new pdf document
PdfDocument doc = new PdfDocument();

// add a new page to the document
PdfPage page = doc.AddPage();

// create a new pdf font
PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
font.Size = 20;

// define a rendering result object
PdfRenderingResult result;

// create a new text element and add it to the page
// Important: get the rendering result returned by Add() 
// into the PdfRenderingResult object
PdfTextElement text = new PdfTextElement(0, 0, "Hello world!", font);
result = page.Add(text);

// add 10 more text element, leaving 30pt between the text lines
for (int i = 1; i <= 10; i++)
{
    PdfTextElement elem = new PdfTextElement(
        0, result.PdfPageLastRectangle.Bottom + 30, "Text line " + i, font);
    result = page.Add(elem);
}

// save pdf document
doc.Save(file);

// close pdf document
doc.Close();
```

```vbnet
' create a new pdf document
Dim doc As New PdfDocument()

' add a new page to the document
Dim page As PdfPage = doc.AddPage()

' create a new pdf font
Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
font.Size = 20

' define a rendering result object
Dim result As PdfRenderingResult

' create a new text element and add it to the page
' Important: get the rendering result returned by Add() 
' into the PdfRenderingResult object
Dim text As New PdfTextElement(0, 0, "Hello world!", font)
result = page.Add(text)

' add 10 more text element, leaving 30pt between the text lines
For i As Integer = 1 To 10
    Dim elem As New PdfTextElement( _
        0, result.PdfPageLastRectangle.Bottom + 30, "Text line " & i, font)
    result = page.Add(elem)
Next

' save pdf document
doc.Save(file)

' close pdf document
doc.Close()
```

## Text elements

Text can be added to a pdf document using `PdfTextElement` objects. There are a lot of settings that can be customized by Select.Pdf library when text is added to a document. Here are some of them:

**Position** of the text element in the destination pdf page can be customize using `PdfTextElement.X` and `PdfTextElement.Y` properties.

**Size** of the destination rectangle where the text will be rendered can be customized using `PdfTextElement.Width` and `PdfTextElement.Height` properties.

**Color** - both foreground and background colors can be customized using the `PdfElement.ForeColor` and `PdfElement.BackColor` properties of the `PdfTextElement` class.

**Font** - font family, size and style can be customized using `PdfTextElement.Font` property.

A font installed on the machine is described with `PdfSystemFont` (family name, size and style) and embedded into the document when it is added to `PdfDocument.Fonts`. Use `PdfSystemFonts` to find out which families are available: `PdfSystemFonts.InstalledFamilies` lists them and `PdfSystemFonts.IsFamilyInstalled(string)` tests for one. A family that is not available is refused with an error rather than replaced by another font. The Liberation Sans, Liberation Serif and Liberation Mono families are bundled with the library and are always available, which makes them a safe choice for code that runs on Windows, Linux and macOS.

**Text alignments** can be controlled using `PdfTextElement.HorizontalAlign` and `PdfTextElement.VerticalAlign` properties.

**Other properties** - `PdfTextElement.CharacterSpacing`, `PdfTextElement.WordSpacing`, `PdfTextElement.RightToLeft`, `PdfTextElement.TextRise`, `PdfTextElement.Leading`.

### Sample Code

Sample code that shows how to create text elements with different settings. Read the comments from the code for customization details.

```csharp
			// create a new pdf document
            PdfDocument doc = new PdfDocument();

            // create a new pdf font (component standard font)
            PdfFont font1 = doc.Fonts.Add(PdfStandardFont.Helvetica);
            font1.Size = 20;

            // create a new pdf font (system font, embedded into the document)
            // Liberation Sans is bundled with the library, so it is available on every host;
            // PdfSystemFonts.IsFamilyInstalled tells whether another family can be used
            string family = PdfSystemFonts.IsFamilyInstalled("Verdana")
                ? "Verdana" : "Liberation Sans";
            PdfFont font2 = doc.Fonts.Add(new PdfSystemFont(family, 15f));

            // add a new page to the document
            PdfPage page = doc.AddPage();

            // define a rendering result object
            PdfRenderingResult result;

            // create text element 
            PdfTextElement text1 = new PdfTextElement(0, 0, 
                "Regular text using standard font Helvetica, size 20.", font1);
            result = page.Add(text1);

            // create text element 
            PdfTextElement text2 = new PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 20, 
                "Regular text using system font " + family + ", size 15.", font2);
            result = page.Add(text2);

            // create colored text element 
            PdfTextElement text3 = new PdfTextElement(
                0, result.PdfPageLastRectangle.Bottom + 20, 200,
                "Red text over light blue background, using system font " + family +
                ", size 15, limited at a width of 200 points.", font2);
            text3.ForeColor = new PdfColor(255, 0, 0);
            text3.BackColor = new PdfColor(173, 216, 230);
            result = page.Add(text3);

            // create colored text element, right aligned
            PdfTextElement text4 = new PdfTextElement(
                0, result.PdfPageLastRectangle.Bottom + 20, 300,
                "Red text over light green background, right aligned, using system font " +
                family + ", size 15, limited at a width of 300 points.", font2);
            text4.ForeColor = new PdfColor(255, 0, 0);
            text4.BackColor = new PdfColor(144, 238, 144);
            text4.HorizontalAlign = PdfTextHorizontalAlign.Right;
            result = page.Add(text4);

            // create text element 
            PdfTextElement text5 = new PdfTextElement(
                0, result.PdfPageLastRectangle.Bottom + 200, 300,
                "Regular text using system font " + family + ", size 15, rotated 45 degrees.",
                font2);
            text5.Direction = 45; // rotate 45 degrees counter-clockwise
            result = page.Add(text5);

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' create a new pdf document
            Dim doc As New PdfDocument()

            ' create a new pdf font (component standard font)
            Dim font1 As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
            font1.Size = 20

            ' create a new pdf font (system font, embedded into the document)
            ' Liberation Sans is bundled with the library, so it is available on every host;
            ' PdfSystemFonts.IsFamilyInstalled tells whether another family can be used
            Dim family As String = If(PdfSystemFonts.IsFamilyInstalled("Verdana"), _
                "Verdana", "Liberation Sans")
            Dim font2 As PdfFont = doc.Fonts.Add(New PdfSystemFont(family, 15F))

            ' add a new page to the document
            Dim page As PdfPage = doc.AddPage()

            ' define a rendering result object
            Dim result As PdfRenderingResult

            ' create text element 
            Dim text1 As New PdfTextElement(0, 0, _
                    "Regular text using standard font Helvetica, size 20.", font1)
            result = page.Add(text1)

            ' create text element 
            Dim text2 As New PdfTextElement(0, _
                    result.PdfPageLastRectangle.Bottom + 20, _
                    "Regular text using system font " + family + ", size 15.", font2)
            result = page.Add(text2)

            ' create colored text element 
            Dim text3 As New PdfTextElement( _
                0, result.PdfPageLastRectangle.Bottom + 20, 200, _
                "Red text over light blue background, using system font " + family + _
                ", size 15, limited at a width of 200 points.", font2)
            text3.ForeColor = New PdfColor(255, 0, 0)
            text3.BackColor = New PdfColor(173, 216, 230)
            result = page.Add(text3)

            ' create colored text element, right aligned
            Dim text4 As New PdfTextElement( _
                0, result.PdfPageLastRectangle.Bottom + 20, 300, _
                "Red text over light green background, right aligned, using system font " + _
                family + ", size 15, limited at a width of 300 points.", font2)
            text4.ForeColor = New PdfColor(255, 0, 0)
            text4.BackColor = New PdfColor(144, 238, 144)
            text4.HorizontalAlign = PdfTextHorizontalAlign.Right
            result = page.Add(text4)

            ' create text element 
            Dim text5 As New PdfTextElement( _
                0, result.PdfPageLastRectangle.Bottom + 200, 300, _
                "Regular text using system font " + family + ", size 15, rotated 45 degrees.", _
                font2)
            text5.Direction = 45
            ' rotate 45 degrees counter-clockwise
            result = page.Add(text5)

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

## Image elements

Images can be added to a pdf document using `PdfImageElement` objects. Here are some of the setting that can be used to customized how an image will look in pdf:

**Position** of the image element in the destination pdf page can be customize using `PdfImageElement.X` and `PdfImageElement.Y` properties.

**Size** of the image in the destination page can be customized using `PdfImageElement.Width` and `PdfImageElement.Height` properties.

**Image source** - the image can be specified as a file name if a constructor with the specific parameter is used: `PdfImageElement.ctor(float, float, string)`

**Transparency** can be customized using `PdfElement.Transparency` property.

### Sample Code

Sample code that shows how to create image elements with different settings. Read the comments from the code for customization details.

```csharp
			// create a new pdf document
            PdfDocument doc = new PdfDocument();

            // add a new page to the document
            PdfPage page = doc.AddPage();

            // get image path
            string imgFile = @"C:\Path\to\logo.png";

            // define a rendering result object
            PdfRenderingResult result;

            // create image element from file path with real image size
            PdfImageElement img1 = new PdfImageElement(0, 0, imgFile);
            result = page.Add(img1);

            // create image element from file path with fixed size in pdf
            // (aspect ratio not preserved)
            PdfImageElement img2 = new PdfImageElement(0,
                result.PdfPageLastRectangle.Bottom + 50, 100, 100, imgFile);
            result = page.Add(img2);

            // add text element
            PdfFont font1 = doc.Fonts.Add(PdfStandardFont.Helvetica);
            font1.Size = 10;
            PdfTextElement text1 = new PdfTextElement(0, 
                result.PdfPageLastRectangle.Bottom + 50, Helper.SomeText(), font1);
            page.Add(text1);

            // add image over text (no transparency)
            PdfImageElement img3 = new PdfImageElement(0, 
                result.PdfPageLastRectangle.Bottom + 50, imgFile);
            result = page.Add(img3);

            // add image over text (with transparency) next to the previous image
            PdfImageElement img4 = new PdfImageElement(
                result.PdfPageLastRectangle.Right + 30, 
                result.PdfPageLastRectangle.Top, imgFile);
            img4.Transparency = 50;
            result = page.Add(img4);

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' create a new pdf document
            Dim doc As New PdfDocument()

            ' add a new page to the document
            Dim page As PdfPage = doc.AddPage()

            ' get image path
            Dim imgFile As String = "C:\Path\to\logo.png"

            ' define a rendering result object
            Dim result As PdfRenderingResult

            ' create image element from file path with real image size
            Dim img1 As New PdfImageElement(0, 0, imgFile)
            result = page.Add(img1)

            ' create image element from file path with fixed size in pdf
            ' (aspect ratio not preserved)
            Dim img2 As New PdfImageElement(0, result.PdfPageLastRectangle.Bottom + 50, _
                                            100, 100, imgFile)
            result = page.Add(img2)

            ' add text element
            Dim font1 As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
            font1.Size = 10
            Dim text1 As New PdfTextElement(0, result.PdfPageLastRectangle.Bottom + 50, _
                                            Helper.SomeText(), font1)
            page.Add(text1)

            ' add image over text (no transparency)
            Dim img3 As New PdfImageElement(0, result.PdfPageLastRectangle.Bottom + 50, imgFile)
            result = page.Add(img3)

            ' add image over text (with transparency) next to the previous image
            Dim img4 As New PdfImageElement(result.PdfPageLastRectangle.Right + 30, _
                                            result.PdfPageLastRectangle.Top, imgFile)
            img4.Transparency = 50
            result = page.Add(img4)

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

## HTML elements

HTML content can be added to a pdf document in several ways. If a complex document is created that will contain several types of pdf elements and the html content that will be added is only a small part of the total content, `PdfHtmlElement` objects can be used to convert web pages or raw html content and add the conversion result to the pdf document.

If the pdf is generated mostly as a result of an html to pdf conversion, the `HtmlToPdf` object should be used instead. This is described in details in the "Html to Pdf Converter" section of our documentation that starts here.

### Element properties

Both full web pages and html strings can be converted to pdf using `PdfHtmlElement` with the appropriate constructor. `PdfHtmlElement.ctor(string)` can be used to convert a full web page specified by an url or local file to pdf. `PdfHtmlElement.ctor(string, string)` can be used to convert a raw html string to pdf.

**Properties**

`PdfHtmlElement.Authentication` - Handles authentication options if the web page being converted requires authentication.

`PdfHtmlElement.CssMediaType` - Indicates what css styles are used when the web page is rendered.

`PdfHtmlElement.DrawBackground` - Gets or sets a flag indicating if the web page background is rendered in pdf.

`PdfHtmlElement.HiddenWebElements` - Gets a reference to the object that controls the visibility of some web elements in the generated pdf document.

`PdfHtmlElement.HttpCookies` - Gets the collection of custom HTTP cookies used for the conversion.

`PdfHtmlElement.HttpHeaders` - Get the collection of custom HTTP headers used for the conversion.

`PdfHtmlElement.JavaScriptEnabled` - Enable scripts when rendering the url or html string.

`PdfHtmlElement.CreateFormFields` - Converts the controls of the html forms into fillable PDF form fields of the document. See "HTML forms to PDF form fields" (html-to-pdf.md).

`PdfHtmlElement.NavigationTimeout` - The time in seconds the converter waits for the web page to load.

`PdfHtmlElement.ConversionDelay` - An additional time in seconds the converter waits after the web page has loaded, before it is rendered.

`PdfHtmlElement.PdfBookmarkOptions` - Gets a reference to the object that controls the bookmarks creation for the generated pdf document.

`PdfHtmlElement.ProxyOptions` - Gets a reference to an object containing the proxy settings used to access the web page that is being converted.

`PdfHtmlElement.StartupMode` - Use this property to specify how the conversion starts.

`PdfHtmlElement.VisibleWebElementSelector` - A css selector. When it is set, only the matched element of the page is rendered by this element, instead of the whole page - see "Partial page conversion" (html-to-pdf.md). `PdfHtmlElement.VisibleWebElementId` is the obsolete form of the same option and accepts an element id only.

`PdfHtmlElement.WebPageWidth` - Gets or sets the width of the converted web page as it would appear in the internal browser used to render the html.

`PdfHtmlElement.WebPageHeight` - Gets or sets the height of the converted web page as it would appear in the internal browser used to render the html.

`PdfHtmlElement.WebPageFixedSize` - Controls whether the content below `PdfHtmlElement.WebPageHeight` is left out.

`PdfHtmlElement.WebPageInformation` - Gets an object populated after conversion with the title, keywords and description of the converted web page.

### Size, pagination and the pages the element creates

`PdfHtmlElement.Width` and `PdfHtmlElement.Height` default to `-1`, which means "as much as there is": the element fills the width available to the right of its `X` and the height available below its `Y`. Give either of them a value to place the render in a rectangle of your own.

An element with an automatic `Height` renders the whole html, wherever it starts: its first page holds what fits in the room left below `Y`, the pages after it are full pages, and the last one ends where the content ends - so an element added under it can start right there. Set `PdfHtmlElement.AllowContentHeightResize` to `false` to draw every page of the render at its full height instead.

Where the content ends is measured by the rendering engine after it prints the page (`ManagedContentTrimmingMode.Ink`, the default), so everything the html paints is kept - a shadow, a positioned decoration - together with the space its last element leaves below itself. Set `PdfHtmlElement.ManagedContentTrimmingMode` to `ManagedContentTrimmingMode.Marker` to end the last page where the page's layout ends, the way earlier versions did, or `PdfHtmlElement.ManagedContentTrimming` to `false` to let the engine shorten its own page instead.

A fixed `Height` that does not fit in what is left of the page continues on the pages that follow and stops once that much height has been drawn.

Content that continues uses the page that **follows** when the document already has one, and a new page is added only when it does not - so two elements that both overflow the same page share the pages the first one created instead of each adding a run of its own. A page added this way carries the features of the page it continues: size, orientation, margins and rotation.

After the element has been added, `PdfHtmlElement.PdfPagesRectangles` reports where each piece of the render was drawn, and the `PdfRenderingResult` returned by `PdfPage.Add(PdfPageElement)` gives the last page and rectangle - which is what you chain the next element off.

> **Note:** The html is rendered in a virtual browser `PdfHtmlElement.WebPageWidth` pixels wide (1024 by default) and the result is scaled to fit `Width`. For a narrow rectangle - a card, a column - that scale makes body text unreadably small, so set `WebPageWidth` to roughly `Width * 4 / 3` instead (for example 400 for a 250pt wide rectangle).

A `PdfHtmlElement` can also be added to a `PdfTemplate` - a header, a footer or a stamp - in which case it renders once and the band is repeated on every page the template covers. Nothing paginates there, since a template is one piece of content stamped on many pages. See "Templates" (pdf-creation-elements.md).

### Sample Code

Sample code that shows how to use `PdfHtmlElement` object to convert a web page to pdf.

```csharp
			// create a new pdf document
            PdfDocument doc = new PdfDocument();

            // add a new page to the document
            PdfPage page = doc.AddPage();

            // create html element 
            PdfHtmlElement html = new PdfHtmlElement(url);

            // add the html element to the document
            page.Add(html);

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' create a new pdf document
	        Dim doc As New PdfDocument()
	
	        ' add a new page to the document
	        Dim page As PdfPage = doc.AddPage()
	
	        ' create html element 
	        Dim html As New PdfHtmlElement(url)
	
	        ' add the html element to the document
	        page.Add(html)
	
	        ' save pdf document
	        doc.Save("Sample.pdf")
	
	        ' close pdf document
	        doc.Close()
```

## Graphic elements

Select.Pdf has support to draw a variety of shapes, such as:

- Circles: `PdfCircleElement`
- Ellipses: `PdfEllipseElement`
- Ellipse Slices: `PdfEllipseSliceElement`
- Ellipse Arcs: `PdfEllipseArcElement`
- Lines: `PdfLineElement`
- Polygons: `PdfPolygonElement`
- Rectangles: `PdfRectangleElement`
- Bezier Curves: `PdfBezierCurveElement`

These graphic elements (all derived from `PdfElement`), share a number of properties that can be used to modify their appearance:

- Line style: `PdfElement.LineStyle` can be used to specify the line styles of the graphic elements that draw lines. Using this, the line width, line dash style, line cap style and line join style can be specified.
- Gradient: `PdfElement.Gradient` can be used to specify the gradient used to fill a shape.
- Line color: `PdfElement.ForeColor` can be used to specify the color of the drawn lines.
- Background color: `PdfElement.BackColor` can be used to specify the background color.
- Color space: `PdfElement.ColorSpace` can be used to write the colors of the element in RGB (the default), CMYK or grayscale.

### Sample Code

The following code shows how to draw several graphic elements:

```csharp
			// create a new pdf document
            PdfDocument doc = new PdfDocument();

            // add a new page to the document
            PdfPage page = doc.AddPage();

            // define a rendering result object
            PdfRenderingResult result;

            // simple horizontal line element
            PdfLineElement line1 = new PdfLineElement(0, 0, 400, 0);
            line1.LineStyle.LineWidth = 3;
            result = page.Add(line1);

            // horizontal dotted red line element
            PdfLineElement line2 = new PdfLineElement(0, 30, 400, 30);
            line2.ForeColor = new PdfColor(255, 0, 0);
            line2.LineStyle.LineWidth = 2;
            line2.LineStyle.LineDashStyle = PdfLineDashStyle.Dot;
            result = page.Add(line2);

            // horizontal dashed blue line element with complex styles
            PdfLineElement line3 = new PdfLineElement(0, 60, 400, 60);
            line3.ForeColor = new PdfColor(0, 0, 255);
            line3.LineStyle.LineWidth = 2;
            line3.LineStyle.LineDashStyle = PdfLineDashStyle.Dash;
            line3.LineStyle.LineCapStyle = PdfLineCapStyle.RoundCap;
            line3.LineStyle.LineJoinStyle = PdfLineJoinStyle.RoundJoin;
            result = page.Add(line3);

            // simple rectangle
            PdfRectangleElement rect1 = new PdfRectangleElement(0, 90, 400, 100);
            result = page.Add(rect1);

            // simple circle
            PdfCircleElement circ1 = new PdfCircleElement(50, 250, 50);
            circ1.ForeColor = new PdfColor(0, 128, 0);
            result = page.Add(circ1);

            // ellipse filled with gradient background color
            PdfEllipseElement elli1 = new PdfEllipseElement(300, 250, 100, 50);
            elli1.ForeColor = new PdfColor(165, 42, 42);
            elli1.Gradient = new PdfGradient(new PdfColor(173, 216, 230),
                new PdfColor(0, 0, 139), PdfGradientDirection.ForwardDiagonal);
            elli1.LineStyle.LineWidth = 2;
            result = page.Add(elli1);

            // bezier curve
            PdfBezierCurveElement bez1 = new PdfBezierCurveElement(
                50, 350, 100, 400, 200, 350, 250, 370);
            result = page.Add(bez1);

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
' create a new pdf document
Dim doc As New PdfDocument()

' add a new page to the document
Dim page As PdfPage = doc.AddPage()

' define a rendering result object
Dim result As PdfRenderingResult

' simple horizontal line element
Dim line1 As New PdfLineElement(0, 0, 400, 0)
line1.LineStyle.LineWidth = 3
result = page.Add(line1)

' horizontal dotted red line element
Dim line2 As New PdfLineElement(0, 30, 400, 30)
line2.ForeColor = New PdfColor(255, 0, 0)
line2.LineStyle.LineWidth = 2
line2.LineStyle.LineDashStyle = PdfLineDashStyle.Dot
result = page.Add(line2)

' horizontal dashed blue line element with complex styles
Dim line3 As New PdfLineElement(0, 60, 400, 60)
line3.ForeColor = New PdfColor(0, 0, 255)
line3.LineStyle.LineWidth = 2
line3.LineStyle.LineDashStyle = PdfLineDashStyle.Dash
line3.LineStyle.LineCapStyle = PdfLineCapStyle.RoundCap
line3.LineStyle.LineJoinStyle = PdfLineJoinStyle.RoundJoin
result = page.Add(line3)

' simple rectangle
Dim rect1 As New PdfRectangleElement(0, 90, 400, 100)
result = page.Add(rect1)

' simple circle
Dim circ1 As New PdfCircleElement(50, 250, 50)
circ1.ForeColor = New PdfColor(0, 128, 0)
result = page.Add(circ1)

' ellipse filled with gradient background color
Dim elli1 As New PdfEllipseElement(300, 250, 100, 50)
elli1.ForeColor = New PdfColor(165, 42, 42)
elli1.Gradient = New PdfGradient(New PdfColor(173, 216, 230), _
    New PdfColor(0, 0, 139), PdfGradientDirection.ForwardDiagonal)
elli1.LineStyle.LineWidth = 2
result = page.Add(elli1)

' bezier curve
Dim bez1 As New PdfBezierCurveElement(50, 350, 100, 400, 200, 350, _
    250, 370)
result = page.Add(bez1)

' save pdf document
doc.Save("Sample.pdf")

' close pdf document
doc.Close()
```

## Page settings

Select.Pdf can be used to control how a page looks like when it is added to a pdf document. The most important properties of a pdf page are:

- Page size
- Page orientation
- Page margins

**Page size**

SelectPdf supports the following standard page sizes:

- A0 to A10
- B0 to B5
- ArchA to ArchE
- Letter, HalfLetter, Letter11x17, Note, Flsa, Ledger

Pages with custom page sizes can also be created using Select.Pdf.

**Page orientation**

SelectPdf supports the following page orientations:

- Portrait
- Landscape

**Page margins**

Page margins can be set using `PdfMargins` object.

### Adding a page to a pdf document

The page settings described above can be set when a new page is added to the pdf document.

```csharp
// add a new page to the document 
// (with specific page size, margins and orientation)
PdfPage page = doc.AddPage(PdfCustomPageSize.A4, new PdfMargins(0f),
    PdfPageOrientation.Portrait);
```

```vbnet
' add a new page to the document 
' (with specific page size, margins and orientation)
Dim page As PdfPage = doc.AddPage(PdfCustomPageSize.A4, New PdfMargins(0.0F), _
    PdfPageOrientation.Portrait)
```

The page size and orientation can also be modified directly on `PdfPage` object, but the page margins can be set only on the `PdfDocument` object, using either `PdfDocument.AddPage(PdfMargins)` method or the `PdfDocument.Margins` property.

A page added with no parameters specified uses the pdf document defaults, not the settings of the page before it: A4 size, Portrait orientation, and the margins of the `PdfDocument.Margins` property (28 points on a new document). Each page keeps the size, orientation and margins it was added with, so a document can mix them freely - pass them to `PdfDocument.AddPage(PdfCustomPageSize, PdfMargins, PdfPageOrientation)` whenever a page has to differ from those defaults.

### Page Rotation

`PdfPage.Rotation` sets how a page is displayed: turned by 90, 180 or 270 degrees (`PdfPageRotation`). The rotation applies to the finished page, so content is placed the same way whether the page is rotated before or after it is drawn. It works on new pages, on the pages of a loaded document and on the pages of a converted document.

```csharp
PdfPage page = doc.AddPage();
page.Rotation = PdfPageRotation.Rotate_90;
```

```vbnet
Dim page As PdfPage = doc.AddPage()
page.Rotation = PdfPageRotation.Rotate_90
```

### Sample Code

The sample code below create a first page with no margins, size A4 and Portrait orientation and a second page with 20pt for all margins, Letter size and Landscape orientation.

```csharp
// create a new pdf document
PdfDocument doc = new PdfDocument();

// create a new pdf font
PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
font.Size = 18;

// add a new page to the document 
// (with specific page size, margins and orientation)
PdfPage page = doc.AddPage(PdfCustomPageSize.A4, new PdfMargins(0f),
    PdfPageOrientation.Portrait);

// create a new text element and add it to the page
PdfTextElement text = new PdfTextElement(0, 0, Helper.SomeText(), font);
page.Add(text);

// add a new page to the document
// (with specific page size, margins and orientation)
PdfPage page2 = doc.AddPage(PdfCustomPageSize.Letter, new PdfMargins(20f),
    PdfPageOrientation.Landscape);

// create a new text element and add it to the page
PdfTextElement text2 = new PdfTextElement(0, 0, Helper.SomeText(), font);
page2.Add(text2);

// save pdf document
doc.Save(file);

// close pdf document
doc.Close();
```

```vbnet
' create a new pdf document
Dim doc As New PdfDocument()

' create a new pdf font
Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
font.Size = 18

' add a new page to the document 
' (with specific page size, margins and orientation)
Dim page As PdfPage = doc.AddPage(PdfCustomPageSize.A4, New PdfMargins(0.0F), _
    PdfPageOrientation.Portrait)

' create a new text element and add it to the page
Dim text As New PdfTextElement(0, 0, Helper.SomeText(), font)
page.Add(text)

' add a new page to the document
' (with specific page size, margins and orientation)
Dim page2 As PdfPage = doc.AddPage(PdfCustomPageSize.Letter, New PdfMargins(20.0F), _
    PdfPageOrientation.Landscape)

' create a new text element and add it to the page
Dim text2 As New PdfTextElement(0, 0, Helper.SomeText(), font)
page2.Add(text2)

' save pdf document
doc.Save(file)

' close pdf document
doc.Close()
```

## Document properties

Select.Pdf allows you to set the basic document information for the generated pdf files:

- Title
- Author
- Subject
- Keywords
- Creation Date

This can be done using `PdfDocument.DocumentInformation` property of the `PdfDocument` object.

### Code Sample

```csharp
// set the document properties
doc.DocumentInformation.Title = "Select.Pdf for .NET Sample";
doc.DocumentInformation.Author = "Select.Pdf Samples";
doc.DocumentInformation.Subject = "Select.Pdf Library Samples";
doc.DocumentInformation.Keywords = "pdf library, sample code";
doc.DocumentInformation.CreationDate = DateTime.Now;
```

```vbnet
' set the document properties
doc.DocumentInformation.Title = "Select.Pdf for .NET Sample"
doc.DocumentInformation.Author = "Select.Pdf Samples"
doc.DocumentInformation.Subject = "Select.Pdf Library Samples"
doc.DocumentInformation.Keywords = "pdf library, sample code"
doc.DocumentInformation.CreationDate = DateTime.Now
```

## Viewer preferences

A pdf document can control the way it will be displayed when opened in a pdf viewer. These properties can be set by Select.Pdf library using `PdfDocument.ViewerPreferences` property of the `PdfDocument` object.

The following customizations can be done using the `PdfViewerPreferences` object:

| Property | Description |
|---|---|
| HideToolbar | A flag specifying whether to hide the pdf viewer application's tool bars when the document is active |
| HideMenuBar | A flag specifying whether to hide the pdf viewer application's menu bar when the document is active. |
| HideWindowUI | A flag specifying whether to hide user interface elements in the document's window (such as scroll bars and navigation controls), leaving only the document's contents displayed. |
| FitWindow | A flag specifying whether to resize the document's window to fit the size of the first displayed page. |
| CenterWindow | A flag specifying whether to position the document's window in the center of the screen. |
| DisplayDocTitle | A flag specifying whether the window's title bar should display the document title. |
| PageMode | The document page mode when the pdf document is opened in a pdf viewer. Possible values: \| UseNone \| Neither document outline (bookmarks) nor thumbnail images are visible. \| \|---\|---\| \| UseOutlines \| Document outline (bookmarks) are visible. \| \| UseThumbs \| Thumbnail images are visible. \| \| FullScreen \| Full-screen mode, with no menu bar, window controls or any other window visible. \| \| UseOC \| Optional content group panel is visible. \| \| UseAttachments \| Document attachments are visible. \| |
| PageLayout | The page layout to be used when the document is opened. Possible values: \| SinglePage \| Displays one page at a time. \| \|---\|---\| \| OneColumn \| Displays the pages in one column. \| \| TwoColumnLeft \| Displays the pages in two columns, with odd-numbered pages on the left. \| \| TwoColumnRight \| Displays the pages in two columns, with odd-numbered pages on the right. \| |
| NonFullScreenPageMode | The document page mode when the pdf viewer application exits the full screen mode. Possible values: \| UseNone \| Neither document outline (bookmarks) nor thumbnail images are visible. \| \|---\|---\| \| UseOutlines \| Document outline (bookmarks) are visible. \| \| UseThumbs \| Thumbnail images are visible. \| \| UseOC \| Optional content group panel is visible. \| |

### Code Sample

```csharp
// set the pdf viewer preferences
doc.ViewerPreferences.CenterWindow = true;
doc.ViewerPreferences.DisplayDocTitle = true;
doc.ViewerPreferences.FitWindow = true;
doc.ViewerPreferences.HideMenuBar = true;
doc.ViewerPreferences.HideToolbar = true;
doc.ViewerPreferences.HideWindowUI = false;

doc.ViewerPreferences.NonFullScreenPageMode = PdfViewerFullScreenExitMode.UseNone;
doc.ViewerPreferences.PageLayout = PdfViewerPageLayout.SinglePage;
doc.ViewerPreferences.PageMode = PdfViewerPageMode.UseNone;
```

```vbnet
' set the pdf viewer preferences
doc.ViewerPreferences.CenterWindow = True
doc.ViewerPreferences.DisplayDocTitle = True
doc.ViewerPreferences.FitWindow = True
doc.ViewerPreferences.HideMenuBar = True
doc.ViewerPreferences.HideToolbar = True
doc.ViewerPreferences.HideWindowUI = False

doc.ViewerPreferences.NonFullScreenPageMode = PdfViewerFullScreenExitMode.UseNone
doc.ViewerPreferences.PageLayout = PdfViewerPageLayout.SinglePage
doc.ViewerPreferences.PageMode = PdfViewerPageMode.UseNone
```

## Templates

As seen in the previous sections, pdf elements need to be added to a specific page of the pdf document. But, there are cases when there is the need to display certain pdf elements in all pages of a pdf document. Select.Pdf has suppport for this, without the need to add those pdf elements in each individual page. This is done using **pdf templates**.

A pdf template belongs to a pdf document and contains elements that will automatically repeat on each page of the pdf document. The main object that handles the pdf template is `PdfTemplate`.

Pdf templates can be added to a pdf document using `PdfTemplateCollection.Add(float, float)` and `PdfTemplateCollection.Add(RectangleF)` methods of the `PdfDocument` class. Using these methods, the location of the template in the pdf pages is specified.

`PdfTemplate` class has several properties that can be used to customize the pdf template:

- `PdfTemplate.Docking` - Gets or sets the template docking settings inside the pdf page.
- `PdfTemplate.Anchoring` - Gets or sets the template anchoring settings inside the pdf page.
- `PdfTemplate.DisplayOnOddPages` - Controls the visibility of the template on the odd numbered pages of the pdf document.
- `PdfTemplate.DisplayOnEvenPages` - Controls the visibility of the template on the even numbered pages of the pdf document.
- `PdfTemplate.DisplayOnFirstPage` - Controls the visibility of the template on the first page of the generated pdf document.
- `PdfTemplate.FirstPageNumber` - Controls the page number for the first page being rendered. The page number is displayed setting a **{page_number}** placeholder in a `PdfTextElement.Text` property of a `PdfTextElement` object that can be added to the template. By default the page numbers start with 1. This can be changed using the `PdfTemplate.FirstPageNumber` property.
- `PdfTemplate.TotalPagesOffset` - Controls the total number of pages offset in the pdf document. The total number of pages is displayed setting a **{total_pages}** placeholder in the `PdfTextElement.Text` property of a `PdfTextElement` object that can be added to the template. The total number of pages can be incremented with a value specified by the `PdfTemplate.TotalPagesOffset` property. This could be useful when the generated pdf will be merged with other documents.
- `PdfTemplate.Foreground` - Controls whether the template is rendered in front of the page main content or not.
- `PdfTemplate.Background` - Controls whether the template is rendered behind the page main content or not.

`PdfDocument` has also 2 special templates: `PdfTemplateCollection.Header` and `PdfTemplateCollection.Footer` to manage custom headers and footers that would appear in all pages of the pdf document. More about this in the next section, that describes pdf document headers and footers.

### Sample Code

This sample code shows how to create a new PDF document using Select.Pdf, how to add several pages and pdf templates to the document.

```csharp
			// create a new pdf document
            PdfDocument doc = new PdfDocument();

            // create a new pdf font
            PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
            font.Size = 24;

            // add a new page to the document
            PdfPage page = doc.AddPage();

            // create a new text element and add it to the page
            PdfTextElement text = new PdfTextElement(0, 0, Helper.SomeLongText(), font);
            page.Add(text);

            // get image path
            string imgFile = @"C:\Path\to\logo.png";

            // add a template containing an image
            // the image should repeat on all pdf pages automatically
            PdfTemplate template1 = doc.Templates.Add(
                new SelectPdf.Universal.Drawing.RectangleF(100, 0, 400, 150));
            PdfImageElement img1 = new PdfImageElement(0, 0, imgFile);
            template1.Add(img1);

            // add another template containing an image behind the existing page elements
            // (under the text) the image should repeat on all pdf pages automatically
            PdfTemplate template2 = doc.Templates.Add(
                new SelectPdf.Universal.Drawing.RectangleF(100, 200, 400, 150));
            template2.Background = true;
            PdfImageElement img2 = new PdfImageElement(0, 0, imgFile);
            template2.Add(img2);

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
	' create a new pdf document
    Dim doc As New PdfDocument()

    ' create a new pdf font
    Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
    font.Size = 24

    ' add a new page to the document
    Dim page As PdfPage = doc.AddPage()

    ' create a new text element and add it to the page
    Dim text As New PdfTextElement(0, 0, Helper.SomeLongText(), font)
    page.Add(text)

    ' get image path
    Dim imgFile As String = "C:\Path\to\logo.png"

    ' add a template containing an image
    ' the image should repeat on all pdf pages automatically
    Dim template1 As PdfTemplate = doc.Templates.Add( _
        New SelectPdf.Universal.Drawing.RectangleF(100, 0, 400, 150))
    Dim img1 As New PdfImageElement(0, 0, imgFile)
    template1.Add(img1)

    ' add another template containing an image behind the existing page elements 
    ' (under the text)
    ' the image should repeat on all pdf pages automatically
    Dim template2 As PdfTemplate = doc.Templates.Add( _
        New SelectPdf.Universal.Drawing.RectangleF(100, 200, 400, 150))
    template2.Background = True
    Dim img2 As New PdfImageElement(0, 0, imgFile)
    template2.Add(img2)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```
