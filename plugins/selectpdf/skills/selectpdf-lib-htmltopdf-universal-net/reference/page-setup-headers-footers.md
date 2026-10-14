# Headers and footers

Converted from the shipped SelectPdf.HtmlToPdf.Universal (Community Edition) v26.5 help topics. C# and VB.NET samples are the official ones.

## Headers and footers

Select.Pdf Html To Pdf Converter can add headers and footers to the generated pdf document using the `HtmlToPdf.Header` and `HtmlToPdf.Footer` properties of the `HtmlToPdf` converter object.

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

- Text - using `PdfTextSection` objects.
- Image - using `PdfImageSection` objects.
- HTML using `PdfHtmlSection` objects.

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

- Text - using `PdfTextSection` objects.
- Image - using `PdfImageSection` objects.
- HTML using `PdfHtmlSection` objects.

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
            Dim text As New PdfTextSection(0, 10,
                "Page: {page_number} of {total_pages}  ",
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
