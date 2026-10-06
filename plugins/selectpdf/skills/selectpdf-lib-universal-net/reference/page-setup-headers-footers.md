# Headers, footers and page numbering

Converted from the shipped SelectPdf.Universal v26.4 help topics. C# and VB.NET samples are the official ones.

## Headers and footers in HTML to PDF

The "Headers and footers" (page-setup-headers-footers.md) page describes how to add headers and footers to a `PdfDocument` using "Templates" (pdf-creation-elements.md). That is the approach to be used if the html to pdf conversion is done using `PdfHtmlElement` objects as described here.

When the pdf conversion is done using the main `HtmlToPdf` converter object, there is another way of adding headers and footers to the generated pdf document. That is done using the `HtmlToPdf.Header` and `HtmlToPdf.Footer` properties of the `HtmlToPdf` converter object.

#### Header Options

To display a custom header in the generated pdf, the `HtmlToPdfOptions.DisplayHeader` property of the `HtmlToPdfOptions` object must be set to *true*. If the DisplayHeader property of the Options object is false, the header will not be displayed and all the options set for the header will have no effect.

The header of the pdf document generated from the html to pdf conversion can be customized using the `HtmlToPdf.Header` property of the `HtmlToPdf` converter object. That is an instance of `PdfHeader` class and has the following properties:

- `PdfHeader.Height` - The height of the pdf document header.
- `PdfHeader.DisplayOnFirstPage` - Controls the visibility of the header on the first page of the generated pdf document.
- `PdfHeader.DisplayOnOddPages` - Controls the visibility of the header on the odd numbered pages of the generated pdf document.
- `PdfHeader.DisplayOnEvenPages` - Controls the visibility of the header on the even numbered pages of the generated pdf document.
- `PdfHeader.FirstPageNumber` - Controls the page number for the first page being rendered.
- `PdfHeader.TotalPagesOffset` - Controls the total number of pages offset in the generated pdf document.

To add content to the pdf header, `PdfHeader` class exposes the `PdfHeader.Add(PdfSectionElement)` method. That can be used to add 3 types of objects to the header:

- Text - using `PdfTextSection` objects. This type of object is very similar with `PdfTextElement` described here.
- Image - using `PdfImageSection` objects. This type of object is very similar with `PdfImageElement` described here.
- HTML using `PdfHtmlSection` objects. This type of object is very similar with `PdfHtmlElement` described here.

Page numbers can be added to pdf document headers using `PdfTextSection` objects. The page number is displayed setting a *{page_number}* placeholder in a `PdfTextSection.Text` property of a `PdfTextSection` object that can be added to the header. By default the page numbers start with 1. This can be changed using the `PdfHeader.FirstPageNumber` property of the `PdfHeader` object.

The total number of pages is displayed setting a *{total_pages}* placeholder in the `PdfTextSection.Text` property of a `PdfTextSection` object that can be added to the header. The total number of pages can be incremented with a value specified by the `PdfHeader.TotalPagesOffset` property of the `PdfHeader` object. This could be useful when the generated pdf will be merged with other documents.

The following code sample shows how to add a custom header to the pdf document generated from a html to pdf conversion:

```csharp
			// instantiate a html to pdf converter object
            HtmlToPdf converter = new HtmlToPdf();

            // header settings
            converter.Options.DisplayHeader = true;
            converter.Header.DisplayOnFirstPage = true;
            converter.Header.DisplayOnOddPages = true;
            converter.Header.DisplayOnEvenPages = true;
            converter.Header.Height = 50;

			// add some html content to the header
            PdfHtmlSection headerHtml = new PdfHtmlSection(headerUrl);
            converter.Header.Add(headerHtml);

			// create a new pdf document converting an url
            PdfDocument doc = converter.ConvertUrl(url);

			// save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' instantiate a html to pdf converter object
            Dim converter As New HtmlToPdf()

            ' header settings
            converter.Options.DisplayHeader = True
            converter.Header.DisplayOnFirstPage = True
            converter.Header.DisplayOnOddPages = True
            converter.Header.DisplayOnEvenPages = True
            converter.Header.Height = 50

			' add some html content to the header
            Dim headerHtml As New PdfHtmlSection(headerUrl)
            converter.Header.Add(headerHtml)
            
            ' create a new pdf document converting an url
            Dim doc As PdfDocument = converter.ConvertUrl(url)

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

#### Footer Options

To display a custom footer in the generated pdf, the `HtmlToPdfOptions.DisplayFooter` property of the `HtmlToPdfOptions` object must be set to *true*. If the DisplayFooter property of the Options object is false, the footer will not be displayed and all the options set for the footer will have no effect.

The footer of the pdf document generated from the html to pdf conversion can be customized using the `HtmlToPdf.Footer` property of the `HtmlToPdf` converter object. That is an instance of `PdfFooter` class and has the following properties:

- `PdfFooter.Height` - The height of the pdf document footer.
- `PdfFooter.DisplayOnFirstPage` - Controls the visibility of the footer on the first page of the generated pdf document.
- `PdfFooter.DisplayOnOddPages` - Controls the visibility of the footer on the odd numbered pages of the generated pdf document.
- `PdfFooter.DisplayOnEvenPages` - Controls the visibility of the footer on the even numbered pages of the generated pdf document.
- `PdfFooter.FirstPageNumber` - Controls the page number for the first page being rendered.
- `PdfFooter.TotalPagesOffset` - Controls the total number of pages offset in the generated pdf document.

To add content to the pdf footer, `PdfFooter` class exposes the `PdfFooter.Add(PdfSectionElement)` method. That can be used to add 3 types of objects to the footer:

- Text - using `PdfTextSection` objects. This type of object is very similar with `PdfTextElement` described here.
- Image - using `PdfImageSection` objects. This type of object is very similar with `PdfImageElement` described here.
- HTML using `PdfHtmlSection` objects. This type of object is very similar with `PdfHtmlElement` described here.

Page numbers can be added to pdf document footer using `PdfTextSection` objects. The page number is displayed setting a *{page_number}* placeholder in a `PdfTextSection.Text` property of a `PdfTextSection` object that can be added to the footer. By default the page numbers start with 1. This can be changed using the `PdfFooter.FirstPageNumber` property of the `PdfFooter` object.

The total number of pages is displayed setting a *{total_pages}* placeholder in the `PdfTextSection.Text` property of a `PdfTextSection` object that can be added to the footer. The total number of pages can be incremented with a value specified by the `PdfFooter.TotalPagesOffset` property of the `PdfFooter` object. This could be useful when the generated pdf will be merged with other documents.

The following code sample shows how to add a custom footer to the pdf document generated from a html to pdf conversion:

```csharp
			// instantiate a html to pdf converter object
            HtmlToPdf converter = new HtmlToPdf();

            // footer settings
            converter.Options.DisplayFooter = true;
            converter.Footer.DisplayOnFirstPage = true;
            converter.Footer.DisplayOnOddPages = true;
            converter.Footer.DisplayOnEvenPages = true;
            converter.Footer.Height = 50;

			// add some html content to the footer
            PdfHtmlSection footerHtml = new PdfHtmlSection(footerUrl);
            converter.Footer.Add(footerHtml);

			// page numbers can be added using a PdfTextSection object
            PdfTextSection text = new PdfTextSection(0, 10,
                "Page: {page_number} of {total_pages}  ",
                PdfFont.CreateFromSystemFont("Arial", 8f));
            text.HorizontalAlign = PdfTextHorizontalAlign.Right;
            converter.Footer.Add(text);

			// create a new pdf document converting an url
            PdfDocument doc = converter.ConvertUrl(url);

			// save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' instantiate a html to pdf converter object
            Dim converter As New HtmlToPdf()

            ' header settings
            converter.Options.DisplayFooter = True
            converter.Footer.DisplayOnFirstPage = True
            converter.Footer.DisplayOnOddPages = True
            converter.Footer.DisplayOnEvenPages = True
            converter.Footer.Height = 50

			' add some html content to the footer
            Dim footerHtml As New PdfHtmlSection(footerUrl)
            converter.Footer.Add(footerHtml)
            
            ' page numbers can be added using a PdfTextSection object
            Dim text As New PdfTextSection(0, 10, _
                "Page: {page_number} of {total_pages}  ", _
                PdfFont.CreateFromSystemFont("Arial", 8F))
            text.HorizontalAlign = PdfTextHorizontalAlign.Right
            converter.Footer.Add(text)
            
            ' create a new pdf document converting an url
            Dim doc As PdfDocument = converter.ConvertUrl(url)

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

#### Setting a Different Header or Footer for a Specific Pdf Page

The `HtmlToPdf.Header` and `HtmlToPdf.Footer` described above are document wide - they are repeated on every page selected by their display options. A single page can override them after the conversion, through the `PdfPage` objects of the generated document:

- `PdfPage.CustomHeader` - A `PdfTemplate` that **replaces** the document header on that page. It is not drawn on any other page.
- `PdfPage.CustomFooter` - The same for the document footer.
- `PdfPage.DisplayHeader` / `PdfPage.DisplayFooter` - Set to *false* to hide the band on that page. The space the band reserves is kept, so the page content stays where it is on every other page.

The template is created with `PdfDocument.AddTemplate(float, float)` and filled with the same elements as any other template - see "Templates" (pdf-creation-elements.md). Give it the height of the corresponding `PdfHeader.Height` / `PdfFooter.Height` so it fills the band exactly.

> **Note:** Adding an element directly to the page instead - for example a `PdfHtmlElement` at `(0, 0)` - does not produce a header. Page coordinates start inside the page margins and **below** the band the document header reserves, so such an element is drawn over the page's own content while the document header is still displayed above it.

The following sample code shows how to customize the header of the 3rd page of a generated pdf document:

```csharp
			// instantiate a html to pdf converter object
            HtmlToPdf converter = new HtmlToPdf();

            // header settings
            converter.Options.DisplayHeader = true;
            converter.Header.DisplayOnFirstPage = true;
            converter.Header.DisplayOnOddPages = true;
            converter.Header.DisplayOnEvenPages = true;
            converter.Header.Height = 50;

			// add some html content to the header
            PdfHtmlSection headerHtml = new PdfHtmlSection(headerUrl);
            converter.Header.Add(headerHtml);

			// create a new pdf document converting an url
            PdfDocument doc = converter.ConvertUrl(url);

            // custom header on page 3 only
            if (doc.Pages.Count >= 3)
            {
                PdfPage page = doc.Pages[2];

                // a template as wide as the page and as tall as the header band
                PdfTemplate customHeader = doc.AddTemplate(page.PageSize.Width, 50);

                PdfHtmlElement customHtml = new PdfHtmlElement(
                    "<div><b>This is the custom header that will appear only on page 3!" +
                    "</b></div>",
                    string.Empty);
                customHeader.Add(customHtml);

                // replaces the document header, on this page only
                page.CustomHeader = customHeader;
            }

            // the document header can also simply be hidden on a given page
            // (the space it reserves is kept, so the page content does not move)
            if (doc.Pages.Count >= 4)
            {
                doc.Pages[3].DisplayHeader = false;
            }

			// save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' instantiate a html to pdf converter object
            Dim converter As New HtmlToPdf()

            ' header settings
            converter.Options.DisplayHeader = True
            converter.Header.DisplayOnFirstPage = True
            converter.Header.DisplayOnOddPages = True
            converter.Header.DisplayOnEvenPages = True
            converter.Header.Height = 50

			' add some html content to the header
            Dim headerHtml As New PdfHtmlSection(headerUrl)
            converter.Header.Add(headerHtml)

            ' create a new pdf document converting an url
            Dim doc As PdfDocument = converter.ConvertUrl(url)

            ' custom header on page 3 only
            If doc.Pages.Count >= 3 Then
                Dim page As PdfPage = doc.Pages(2)

                ' a template as wide as the page and as tall as the header band
                Dim customHeader As PdfTemplate = doc.AddTemplate(page.PageSize.Width, 50)

                Dim customHtml As New PdfHtmlElement( _
                    "<div><b>This is the custom header that will appear only on page 3!" & _
                    "</b></div>", _
                    String.Empty)
                customHeader.Add(customHtml)

                ' replaces the document header, on this page only
                page.CustomHeader = customHeader
            End If

            ' the document header can also simply be hidden on a given page
            ' (the space it reserves is kept, so the page content does not move)
            If doc.Pages.Count >= 4 Then
                doc.Pages(3).DisplayHeader = False
            End If

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

## Headers and footers

Headers and Footers are custom types of pdf templates. They can be handled using the `PdfTemplateCollection.Header` and `PdfTemplateCollection.Footer` properties of the `PdfDocument` object.

Header is displayed at the top of the page. Footer is displayed at the bottom of the page. They contains elements that repeat on each page of the pdf document.

### Sample Code

This sample code shows how to create a new PDF document using Select.Pdf, how to add several pages and set the header and footer of the pdf document.

```csharp
	// create a new pdf document
    PdfDocument doc = new PdfDocument();
    doc.Margins = new PdfMargins(10, 10, 0, 0);

    // create a new pdf font
    PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
    font.Size = 24;

    // create a new pdf font
    PdfFont font2 = doc.Fonts.Add(PdfStandardFont.Helvetica);
    font2.Size = 12;
    font2.IsUnderline = true;

    // add a new page to the document
    PdfPage page = doc.AddPage();

    // get image path
    string imgFile = @"C:\Path\to\logo.png";

    // header template (100 points in height) with image element
    doc.Templates.Header = doc.Templates.Add(doc.Pages[0].ClientRectangle.Width, 100);
    PdfImageElement img1 = new PdfImageElement(0, 0, imgFile);
    doc.Templates.Header.Add(img1);

    // footer template (100 points in height) with text element
    doc.Templates.Footer = doc.Templates.Add(doc.Pages[0].ClientRectangle.Width, 100);
    PdfTextElement text1 = new PdfTextElement(0, 50, 
        "Footer text: Document generated by Select.Pdf", font2);
    text1.ForeColor = new PdfColor(0, 0, 255);
    doc.Templates.Footer.Add(text1);

    // create a new text element and add it to the page if page elements are added 
    // after header and footer is set, they will not be displayed in those areas.
    PdfTextElement text = new PdfTextElement(0, 0, Helper.SomeLongText(), font);
    page.Add(text);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
	' create a new pdf document
    Dim doc As New PdfDocument()
    doc.Margins = New PdfMargins(10, 10, 0, 0)

    ' create a new pdf font
    Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
    font.Size = 24

    ' create a new pdf font
    Dim font2 As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
    font2.Size = 12
    font2.IsUnderline = True

    ' add a new page to the document
    Dim page As PdfPage = doc.AddPage()

    ' get image path
    Dim imgFile As String = "C:\Path\to\logo.png"

    ' header template (100 points in height) with image element
    doc.Templates.Header = doc.Templates.Add(doc.Pages(0).ClientRectangle.Width, 100)
    Dim img1 As New PdfImageElement(0, 0, imgFile)
    doc.Templates.Header.Add(img1)

    ' footer template (100 points in height) with text element
    doc.Templates.Footer = doc.Templates.Add(doc.Pages(0).ClientRectangle.Width, 100)
    Dim text1 As New PdfTextElement(0, 50, _
            "Footer text: Document generated by Select.Pdf", font2)
    text1.ForeColor = New PdfColor(0, 0, 255)
    doc.Templates.Footer.Add(text1)

    ' create a new text element and add it to the page
    ' if page elements are added after header and footer is set, 
    ' they will not be displayed in those areas.
    Dim text As New PdfTextElement(0, 0, Helper.SomeLongText(), font)
    page.Add(text)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Page numbering

Large pdf documents usually display page numbers in the footer to make it easier for users to identify content in the pdf document. Select.Pdf offers support for page numbering using special placeholders that can be included in text elements that repeat in all pdf pages using pdf templates.

There are 2 placeholders that can be included in any `PdfTextElement` that belongs to a template:

- **{page_number}** - represents the current page of the pdf document.
- **{total_pages}** - represents the total number of pages of the pdf document.

A few properties of the `PdfTemplate` class can be used to customize page numbering:

- `PdfTemplate.FirstPageNumber` - Controls the page number for the first page being rendered. The page number is displayed setting a **{page_number}** placeholder in a `PdfTextElement.Text` property of a `PdfTextElement` object that can be added to the template. By default the page numbers start with 1. This can be changed using the `PdfTemplate.FirstPageNumber` property.
- `PdfTemplate.TotalPagesOffset` - Controls the total number of pages offset in the pdf document. The total number of pages is displayed setting a **{total_pages}** placeholder in the `PdfTextElement.Text` property of a `PdfTextElement` object that can be added to the template. The total number of pages can be incremented with a value specified by the `PdfTemplate.TotalPagesOffset` property. This could be useful when the generated pdf will be merged with other documents.

### Sample Code

This sample code shows how to create a new PDF document using Select.Pdf, how to add several pages and automatic page numbering in the document footer.

```csharp
			// create a new pdf document
            PdfDocument doc = new PdfDocument();
            doc.Margins = new PdfMargins(10, 10, 0, 0);

            // create a new pdf font
            PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
            font.Size = 24;

            // add a new page to the document
            PdfPage page = doc.AddPage();

            // footer template (100 points in height) with text element
            doc.Templates.Footer = doc.Templates.Add(doc.Pages[0].ClientRectangle.Width, 100);
            PdfTextElement text1 = new PdfTextElement(0, 50, 
                "Page: {page_number} of {total_pages}.", font);
            text1.ForeColor = new PdfColor(0, 0, 255);
            doc.Templates.Footer.Add(text1);

            // create a new text element and add it to the page
            // if page elements are added after header and footer is set, 
            // they will not be displayed in those areas.
            PdfTextElement text = new PdfTextElement(0, 0, Helper.SomeLongText(), font);
            page.Add(text);

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
	' create a new pdf document
    Dim doc As New PdfDocument()
    doc.Margins = New PdfMargins(10, 10, 0, 0)

    ' create a new pdf font
    Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
    font.Size = 24

    ' add a new page to the document
    Dim page As PdfPage = doc.AddPage()

    ' footer template (100 points in height) with text element
    doc.Templates.Footer = doc.Templates.Add(doc.Pages(0).ClientRectangle.Width, 100)
    Dim text1 As New PdfTextElement(0, 50, "Page: {page_number} of {total_pages}.", font)
    text1.ForeColor = New PdfColor(0, 0, 255)
    doc.Templates.Footer.Add(text1)

    ' create a new text element and add it to the page
    ' if page elements are added after header and footer is set, 
    ' they will not be displayed in those areas.
    Dim text As New PdfTextElement(0, 0, Helper.SomeLongText(), font)
    page.Add(text)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Headers and footers on existing PDFs

SelectPdf Library for .NET can be used to add headers and footers to existing PDF documents. With existing documents, the method described in "Headers and footers" (page-setup-headers-footers.md) section cannot be used. That only works with newly created PDF files.

To add headers and footers to existing PDF documents, first a resize of the content is performed (using the method described in "Resizing pages" (edit-merge-split-stamp.md) section) to create space for the header/footer and then PDF templates are added (using the method described in "Templates" (pdf-creation-elements.md) section).

### Code Sample

The sample code below shows how to modify an existing PDF document using SelectPdf to make space for headers and footers and how to add the header and footer to the existing pdf document.

```csharp
// the test file
string filePdf = @"C:\Path\to\document.pdf";
string imgFile = @"C:\Path\to\logo.png";

// resize the content
PdfResizeManager resizer = new PdfResizeManager();
resizer.Load(filePdf);

// add extra top and bottom margins
resizer.PageMargins = new PdfMargins(0, 0, 90, 40);

// add the header and footer to the existing (now resized pdf document)
PdfDocument doc = resizer.GetDocument();

// header template (90 points in height) with image element
PdfTemplate header = doc.Templates.Add(doc.Pages[0].ClientRectangle.Width, 90);
PdfImageElement img1 = new PdfImageElement(10, 10, imgFile);
header.Add(img1);

// footer template (40 points in height) with text element
PdfTemplate footer = doc.Templates.Add(new SelectPdf.Universal.Drawing.RectangleF(0, 
    doc.Pages[0].ClientRectangle.Height - 40, 
    doc.Pages[0].ClientRectangle.Width, 40));

// create a new pdf font
PdfFont font2 = doc.Fonts.Add(PdfStandardFont.Helvetica);
font2.Size = 12;

PdfTextElement text1 = new PdfTextElement(10, 10,
    "Generated by SelectPdf. Page number {page_number} of {total_pages}.", 
    font2);
text1.ForeColor = new PdfColor(0, 0, 255);
footer.Add(text1);

// save pdf document
doc.Save("Sample.pdf");

// close pdf document
resizer.Close();
```

```vbnet
' the test file
Dim filePdf As String = "C:\Path\to\document.pdf"
Dim imgFile As String = "C:\Path\to\logo.png"

' resize the content
Dim resizer As New PdfResizeManager()
resizer.Load(filePdf)

' add extra top and bottom margins
resizer.PageMargins = New PdfMargins(0, 0, 90, 40)

' add the header and footer to the existing (now resized pdf document)
Dim doc As PdfDocument = resizer.GetDocument()

' header template (90 points in height) with image element
Dim header As PdfTemplate = doc.Templates.Add(doc.Pages(0).ClientRectangle.Width, 90)
Dim img1 As New PdfImageElement(10, 10, imgFile)
header.Add(img1)

' footer template (40 points in height) with text element
Dim footer As PdfTemplate = doc.Templates.Add( _
    New SelectPdf.Universal.Drawing.RectangleF(0, _
        doc.Pages(0).ClientRectangle.Height - 40, _
        doc.Pages(0).ClientRectangle.Width, 40))

' create a new pdf font
Dim font2 As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
font2.Size = 12

Dim text1 As New PdfTextElement(10, 10, _
    "Generated by SelectPdf. Page number {page_number} of {total_pages}.", font2)
text1.ForeColor = New PdfColor(0, 0, 255)
footer.Add(text1)

' save pdf document
doc.Save("Sample.pdf")

' close pdf document
resizer.Close()
```
