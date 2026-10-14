# Modifying existing PDFs: merge, resize, stamps, portfolios, compression

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## Modifying an existing PDF

Select.Pdf Library for .NET can be used to load and modify existing pdf documents. The following sample code shows how to load a pdf document from a local file from the disk:

```csharp
	// load pdf file from disk
    PdfDocument doc = new PdfDocument(fileName);
```

```vbnet
	' load pdf file from disk
    Dim doc As New PdfDocument(fileName)
```

The `PdfDocument` object also provides constructors that can be used to load the pdf document from a stream.

Once a pdf document is loaded, all operations described in this documentation can applied to it: add new pages to the document, remove existing pages, add new elements to new or existing pages, add new pdf templates.

### Sample Code

This sample code shows how to use SelectPdf Library for .NET to load an existing document, add a new page to it and a new text element.

```csharp
	// the initial file
    string file = @"C:\Path\to\doc1.pdf";

    // load the pdf document
    PdfDocument doc = new PdfDocument(file);

    // add a new page to the document
    PdfPage page = doc.AddPage();

    // create a new pdf font (component standard font)
    PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
    font.Size = 20;

    // create text element and add it to the new page
    PdfTextElement text = new PdfTextElement(100, 100,
        "Sample text added to an existing pdf document.", font);
    page.Add(text);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
        	' the initial file
	        Dim file As String = "C:\Path\to\doc1.pdf"
	
	        ' load the pdf document
	        Dim doc As New PdfDocument(file)
	
	        ' add a new page to the document
	        Dim page As PdfPage = doc.AddPage()
	
	        ' create a new pdf font (component standard font)
	        Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
	        font.Size = 20
	
	        ' create text element and add it to the new page
	        Dim text As New PdfTextElement(100, 100, _
	                "Sample text added to an existing pdf document.", font)
	        page.Add(text)
	
	        ' save pdf document
	        doc.Save("Sample.pdf")
	
	        ' close pdf document
	        doc.Close()
```

## Merging PDFs

Select.Pdf can be used to merge several existing pdf documents into a single pdf document.

To achive this task, individual pdf files need to be loaded into `PdfDocument` objects. The following sample code shows how to load an existing file from the disk:

```csharp
	// load pdf file from disk
    PdfDocument doc = new PdfDocument(fileName);
```

```vbnet
	' load pdf file from disk
    Dim doc As New PdfDocument(fileName)
```

Another important method that will be used by the pdf merge feature is `PdfDocument.Append(PdfDocument)` of the `PdfDocument` object. Using this method, a pdf document can be appended to another pdf document:

```csharp
	// append doc1 to doc2
    doc2.Append(doc1);
```

```vbnet
	' append doc1 to doc2
    doc2.Append(doc1)
```

### Sample Code

This sample code shows how to use SelectPdf Library for .NET to merge 2 different pdf documents into a new pdf document.

```csharp
	// the 2 initial files
    string file1 = @"C:\Path\to\doc1.pdf";
    string file2 = @"C:\Path\to\doc2.pdf";

    // load the 2 pdf documents
    PdfDocument doc1 = new PdfDocument(file1);
    PdfDocument doc2 = new PdfDocument(file2);

    // create a new pdf document
    PdfDocument doc = new PdfDocument();

    // add the pages of those 2 documents to the new document
    doc.Append(doc1);
    doc.Append(doc2);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();

    // close the original pdf documents
    doc1.Close();
    doc2.Close();
```

```vbnet
	' the 2 initial files
    Dim file1 As String = "C:\Path\to\doc1.pdf"
    Dim file2 As String = "C:\Path\to\doc2.pdf"

    ' load the 2 pdf documents
    Dim doc1 As New PdfDocument(file1)
    Dim doc2 As New PdfDocument(file2)

    ' create a new pdf document
    Dim doc As New PdfDocument()

    ' add the pages of those 2 documents to the new document
    doc.Append(doc1)
    doc.Append(doc2)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()

    ' close the original pdf documents
    doc1.Close()
    doc2.Close()
```

### Merging inputs in different shapes

The `Append` method of the `PdfDocument` class accepts inputs in different shapes - a file path, a `Stream`, or another `PdfDocument`. Calling `Append` repeatedly on a target document combines all the inputs into it:

- `PdfDocument.Append(string)`
- `PdfDocument.Append(Stream)`
- `PdfDocument.Append(PdfDocument)`

```csharp
	// merge a file and a stream into a new document
    using (FileStream fs = File.OpenRead("second.pdf"))
    {
        PdfDocument merged = new PdfDocument();

        merged.Append("first.pdf");
        merged.Append(fs);

        merged.Save("combined.pdf");
        merged.Close();
    }
```

```vbnet
	' merge a file and a stream into a new document
    Using fs As FileStream = File.OpenRead("second.pdf")
        Dim merged As New PdfDocument()

        merged.Append("first.pdf")
        merged.Append(fs)

        merged.Save("combined.pdf")
        merged.Close()
    End Using
```

### Merging tagged (accessible) documents

Appending into a tagged document carries the appended document's logical structure across, so the merged document keeps a reading order that covers all of its pages and stays conformant. Appending into a document that is not tagged leaves the structure behind - the destination makes no accessibility claim, and the appended structure would be the only structure in it.

See "Accessible PDF and PDF/A" (accessibility-and-compliance.md) for tagged and PDF/A output.

### Merging into PDF/A documents

A document created with a PDF/A standard (`PdfDocument.ctor(PdfStandard)`) takes pages only from documents that conform to its level: the same PDF/A part with at least the same conformance (for example a PDF/A-3a or PDF/A-3u source into a PDF/A-3b document). Appending a document that claims no PDF/A conformance, or another part, throws an exception rather than producing a file whose PDF/A claim is false. A document with no PDF/A standard takes any source.

### Appending to an existing file

Pages can also be appended to a document loaded from a file or a stream, and the result saved back to the same file.

```csharp
PdfDocument doc = new PdfDocument("report.pdf");
doc.Append("appendix.pdf");
doc.Save("report.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument("report.pdf")
doc.Append("appendix.pdf")
doc.Save("report.pdf")
doc.Close()
```

## Resizing pages

SelectPdf PDF Library for .NET can be used to modify existing PDF documents and resize the content of the pages (change PDF page size, change PDF page orientation, add margins to the PDF pages).

The PDF resize or scaling can be done using `PdfResizeManager`. `PdfResizeManager` exposes the following methods:

- `PdfResizeManager.Load(string)` - can be used to load the existing PDF document, password protected or not, from a file, Stream, byte array or `PdfDocument` object.
- `PdfResizeManager.Save` - can be used to save the modified PDF document to a file, Stream, byte array or directly into the browser's response Stream (for web applications).
- `PdfResizeManager.GetDocument` - returns the modified PDF document as a `PdfDocument` object.

`PdfResizeManager` exposes the following properties that can be used to customize the way content is resized/scaled:

- `PdfResizeManager.PageSize` - The generated document (resized) page size. The default value is A4.
- `PdfResizeManager.PageOrientation` - The generated document (resized) page orientation. The default value is Portrait.
- `PdfResizeManager.PageMargins` - The generated document (resized) page margins. The default value is 0 for all margins. Important: Margins specified are added to the existing margins.
- `PdfResizeManager.AllowScale` - Specifies if the content can be scaled (resized) in the generated document (resized).
- `PdfResizeManager.KeepAspectRatio` - Specifies if the content aspect ratio (proportion) is preserved in the generated document (resized).
- `PdfResizeManager.HorizontalAlign` - Specifies how the content is horizontally aligned in the destination rectangle, when the aspect ratio is preserved. This is taken into consideration only if the content is rendered at (0,0) in the client rectangle, scaling is allowed and aspect ratio is preserved.
- `PdfResizeManager.VerticalAlign` - Specifies how the content is vertically aligned in the destination rectangle, when the aspect ratio is preserved. This is taken into consideration only if the content is rendered at (0,0) in the client rectangle, scaling is allowed and aspect ratio is preserved.
- `PdfResizeManager.StartPosition` - The start position of the content in the generated document (resized). By default, this is (0,0) in the client rectangle.
- `PdfResizeManager.ContentSize` - The size of the content in the generated document (resized). By default, this is the client rectange, minus page margins.
- `PdfResizeManager.KeepOriginalPageSettings` - When `true`, the original per-page size and orientation of the input document are preserved, and only the other resize properties (margins, alignment, etc.) are applied. Useful when the input mixes page sizes (e.g. an A4 body with A3 foldouts) and a single uniform output size would distort some pages. The default value is `false`.
- `PdfResizeManager.ViewerPreferences` - The viewer preferences of the generated document. They start as the preferences of the loaded document and can be changed before saving.
- `PdfResizeManager.Security` - The security settings (passwords and permissions) of the generated document. They start as the settings of the loaded document. They are applied when `PdfResizeManager.KeepOriginalPageSettings` is `true` or when any of them is changed; otherwise the generated document is not encrypted.

### What the Resized Document Keeps

Resizing reflows the same document, so the generated document keeps what belongs to it: links and internal link destinations, bookmarks, document information, viewer preferences, attachments, named destinations, the open action, page labels and the XMP metadata. Form fields are carried and stay fillable. A document that conforms to PDF/A, PDF/UA or PDF/X-1a is produced to the same standards, with its logical structure carried onto the resized pages; a document that declares an accessibility standard it does not meet is produced without that declaration.

### Code Sample

The code sample below shows how to use SelectPdf Library for .NET to resize the pages of an existing PDF document, add margins, change page orientation. That can be done keeping the aspect ratio of the content or not, allowing scaling or not.

```csharp
// the test file
string filePdf = @"C:\Path\to\document.pdf";

// settings
string pdf_page_size = pageSizeValue;
PdfPageSize pdfPageSize = (PdfPageSize)Enum.Parse(typeof(PdfPageSize),
    pdf_page_size, true);

PdfCustomPageSize pageSize = PdfCustomPageSize.A4;
switch (pdfPageSize) {
    case PdfPageSize.A1:
        pageSize = PdfCustomPageSize.A1;
        break;
    case PdfPageSize.A2:
        pageSize = PdfCustomPageSize.A2;
        break;
    case PdfPageSize.A3:
        pageSize = PdfCustomPageSize.A3;
        break;
    case PdfPageSize.A5:
        pageSize = PdfCustomPageSize.A5;
        break;
    case PdfPageSize.Letter:
        pageSize = PdfCustomPageSize.Letter;
        break;
    case PdfPageSize.HalfLetter:
        pageSize = PdfCustomPageSize.HalfLetter;
        break;
    case PdfPageSize.Ledger:
        pageSize = PdfCustomPageSize.Ledger;
        break;
    case PdfPageSize.Legal:
        pageSize = PdfCustomPageSize.Legal;
        break;
}

string pdf_orientation = pageOrientationValue;
PdfPageOrientation pdfOrientation =
    (PdfPageOrientation)Enum.Parse(typeof(PdfPageOrientation),
    pdf_orientation, true);

int topMargin = 0;
try
{
    topMargin = Convert.ToInt32(topMarginValue);
}
catch { }

int rightMargin = 0;
try
{
    rightMargin = Convert.ToInt32(rightMarginValue);
}
catch { }

int bottomMargin = 0;
try
{
    bottomMargin = Convert.ToInt32(bottomMarginValue);
}
catch { }

int leftMargin = 0;
try
{
    leftMargin = Convert.ToInt32(leftMarginValue);
}
catch { }

string horizontal_align = horizontalAlignValue;
PdfHorizontalAlign pdfHorizontalAlign =
    (PdfHorizontalAlign)Enum.Parse(typeof(PdfHorizontalAlign),
    horizontal_align, true);

string vertical_align = verticalAlignValue;
PdfVerticalAlign pdfVerticalAlign =
    (PdfVerticalAlign)Enum.Parse(typeof(PdfVerticalAlign),
    vertical_align, true);

// resize the content
PdfResizeManager resizer = new PdfResizeManager();
resizer.Load(filePdf);

resizer.PageMargins = new PdfMargins(
    leftMargin, rightMargin, topMargin, bottomMargin);
resizer.PageSize = pageSize;
resizer.PageOrientation = pdfOrientation;
resizer.AllowScale = allowScaling;
resizer.KeepAspectRatio = keepAspectRatio;
resizer.HorizontalAlign = pdfHorizontalAlign;
resizer.VerticalAlign = pdfVerticalAlign;

// save pdf document
resizer.Save("Sample.pdf");

// close pdf document
resizer.Close();
```

```vbnet
' the test file
Dim filePdf As String = "C:\Path\to\document.pdf"

' settings
Dim pdf_page_size As String = pageSizeValue
Dim pdfPageSize1 As PdfPageSize = _
    DirectCast([Enum].Parse(GetType(PdfPageSize), pdf_page_size, True),  _
        PdfPageSize)

Dim pageSize As PdfCustomPageSize = PdfCustomPageSize.A4
Select Case pdfPageSize1
    Case PdfPageSize.A1
        pageSize = PdfCustomPageSize.A1
        Exit Select
    Case PdfPageSize.A2
        pageSize = PdfCustomPageSize.A2
        Exit Select
    Case PdfPageSize.A3
        pageSize = PdfCustomPageSize.A3
        Exit Select
    Case PdfPageSize.A5
        pageSize = PdfCustomPageSize.A5
        Exit Select
    Case PdfPageSize.Letter
        pageSize = PdfCustomPageSize.Letter
        Exit Select
    Case PdfPageSize.HalfLetter
        pageSize = PdfCustomPageSize.HalfLetter
        Exit Select
    Case PdfPageSize.Ledger
        pageSize = PdfCustomPageSize.Ledger
        Exit Select
    Case PdfPageSize.Legal
        pageSize = PdfCustomPageSize.Legal
        Exit Select
End Select

Dim pdf_orientation As String = pageOrientationValue
Dim pdfOrientation As PdfPageOrientation = _
    DirectCast([Enum].Parse(GetType(PdfPageOrientation), pdf_orientation, True),  _
        PdfPageOrientation)

Dim topMargin As Integer = 0
Try
    topMargin = Convert.ToInt32(topMarginValue)
Catch
End Try

Dim rightMargin As Integer = 0
Try
    rightMargin = Convert.ToInt32(rightMarginValue)
Catch
End Try

Dim bottomMargin As Integer = 0
Try
    bottomMargin = Convert.ToInt32(bottomMarginValue)
Catch
End Try

Dim leftMargin As Integer = 0
Try
    leftMargin = Convert.ToInt32(leftMarginValue)
Catch
End Try

Dim horizontal_align As String = horizontalAlignValue
Dim pdfHorizontalAlign As PdfHorizontalAlign = _
    DirectCast([Enum].Parse(GetType(PdfHorizontalAlign), _
        horizontal_align, True), PdfHorizontalAlign)

Dim vertical_align As String = verticalAlignValue
Dim pdfVerticalAlign As PdfVerticalAlign = _
    DirectCast([Enum].Parse(GetType(PdfVerticalAlign), _
        vertical_align, True), PdfVerticalAlign)

' resize the content
Dim resizer As New PdfResizeManager()
resizer.Load(filePdf)

resizer.PageMargins = New PdfMargins( _
    leftMargin, rightMargin, topMargin, bottomMargin)
resizer.PageSize = pageSize
resizer.PageOrientation = pdfOrientation
resizer.AllowScale = allowScaling
resizer.KeepAspectRatio = keepAspectRatio
resizer.HorizontalAlign = pdfHorizontalAlign
resizer.VerticalAlign = pdfVerticalAlign

' save pdf document
resizer.Save("Sample.pdf")

' close pdf document
resizer.Close()
```

## Stamps and watermarks

Stamps and Watermarks are done using pdf templates that contain elements that repeat on each page of the pdf document.

### Pdf Stamps Sample Code

This sample code shows how to create a new PDF document using Select.Pdf, how to add several pages and stamp all the pages with an image.

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

    // stamp all pages - add a template containing an image to the bottom right of 
    // the page the image should repeat on all pdf pages automatically
    PdfTemplate template = doc.Templates.Add(doc.Pages[0].ClientRectangle);
    PdfImageElement img = new PdfImageElement(
        doc.Pages[0].ClientRectangle.Width - 300, 
        doc.Pages[0].ClientRectangle.Height - 150, imgFile);
    template.Add(img);

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

    ' stamp all pages - add a template containing an image to the bottom right of
    ' the page the image should repeat on all pdf pages automatically
    Dim template As PdfTemplate = doc.Templates.Add(doc.Pages(0).ClientRectangle)
    Dim img As New PdfImageElement(doc.Pages(0).ClientRectangle.Width - 300, _
                                   doc.Pages(0).ClientRectangle.Height - 150, imgFile)
    template.Add(img)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

### Pdf Watermarks Sample Code

This sample code shows how to create a new PDF document using Select.Pdf, how to add several pages and watermark all the pages with a transparent image in the background.

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

    // watermark all pages - add a template containing an image 
    // to the bottom right of the page
    // the image should repeat on all pdf pages automatically
    // the template should be rendered behind the rest of the page elements
    PdfTemplate template = doc.Templates.Add(doc.Pages[0].ClientRectangle);
    PdfImageElement img = new PdfImageElement(
        doc.Pages[0].ClientRectangle.Width - 300, 
        doc.Pages[0].ClientRectangle.Height - 150, imgFile);
    img.Transparency = 50;
    template.Background = true;
    template.Add(img);

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

    ' watermark all pages - add a template containing an image 
    ' to the bottom right of the page
    ' the image should repeat on all pdf pages automatically
    ' the template should be rendered behind the rest of the page elements
    Dim template As PdfTemplate = doc.Templates.Add(doc.Pages(0).ClientRectangle)
    Dim img As New PdfImageElement(doc.Pages(0).ClientRectangle.Width - 300, _
                                   doc.Pages(0).ClientRectangle.Height - 150, imgFile)
    img.Transparency = 50
    template.Background = True
    template.Add(img)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## PDF portfolios

A PDF Portfolio contains multiple files assembled into an integrated PDF unit. The files in a PDF Portfolio can be in a wide range of file types created in different applications. For example, a PDF Portfolio can include text documents, e-mail messages, spreadsheets, CAD drawings, and PowerPoint presentations. The original files retain their individual identities but are assembled into one PDF Portfolio file. You can open, read, edit, and format each component file independently of the other component files in the PDF Portfolio.

PDF portfolios can be handled in .NET applications using SelectPdf with `PdfPortfolioManager` object. Using `PdfPortfolioManager` the following tasks can be performed:

- Create new PDF portfolio from scratch. Use `PdfPortfolioManager.ctor`.
- Manage existing PDF portfolios. Use `PdfPortfolioManager.ctor` and then `PdfPortfolioManager.Load(string)` to load the existing file.
- Transform an existing PDF document into a PDF portfolio. The old PDF will server as a cover page for the PDF portfolio. Use `PdfPortfolioManager.ctor` and then `PdfPortfolioManager.Load(string)` to load the existing file.
- Manage embedded files from the PDF portfolio (list files and their properties, add new files, delete existing files). Access files through `PdfPortfolioManager.Files` property. For embedded PDF portfolio files, use `PdfPortfolioFile` objects to manage name, description, creation and modification date or get the file content using `PdfPortfolioFile.Content` and `PdfPortfolioFile.ContentAsStream` properties and save them as external files. Files are added with `PdfPortfolioFilesCollection.Add(PdfPortfolioFile)` and removed with `PdfPortfolioFilesCollection.Remove(int)` (by index).
- Manage the PDF portfolio view mode. Use `PdfPortfolioManager.ViewMode` property - set it to `PdfPortfolioViewMode.Details` for a list view (name, description, dates) or `PdfPortfolioViewMode.Tile` for a thumbnail grid.
- Manage the PDF portfolio startup file. Use `PdfPortfolioManager.StartupFileName` property.

### Code Sample

The following sample code shows how to create a new PDF portfolio, add 3 embedded files, set the view mode and startup file and save the newly created PDF portfolio on disk.

```csharp
// the test files
string filePdf = @"C:\Path\to\document.pdf";
string fileHtml = @"C:\Path\to\document.html";
string fileImage = @"C:\Path\to\logo.png";

// instantiate the pdf portfolio manager
PdfPortfolioManager portfolio = new PdfPortfolioManager();

// add files to the portfolio
PdfPortfolioFile pdfFilePdf = new PdfPortfolioFile(filePdf);
pdfFilePdf.Description = "PDF document";
portfolio.Files.Add(pdfFilePdf);

PdfPortfolioFile pdfFileHtml = new PdfPortfolioFile(fileHtml);
pdfFileHtml.Description = "HTML document";
portfolio.Files.Add(pdfFileHtml);

PdfPortfolioFile pdfFileImage = new PdfPortfolioFile(fileImage);
pdfFileImage.Description = "Image file";
portfolio.Files.Add(pdfFileImage);

// set view mode
portfolio.ViewMode = PdfPortfolioViewMode.Details;

// set startup file
portfolio.StartupFileName = "logo.png";

// save pdf document
portfolio.Save("Sample.pdf");

// close pdf document
portfolio.Close();
```

```vbnet
	        ' the test files
	        Dim filePdf As String = "C:\Path\to\document.pdf"
	        Dim fileHtml As String = "C:\Path\to\document.html"
	        Dim fileImage As String = "C:\Path\to\logo.png"
	
	        ' instantiate the pdf portfolio manager
	        Dim portfolio As New PdfPortfolioManager()
	
	        ' add files to the portfolio
	        Dim pdfFilePdf As New PdfPortfolioFile(filePdf)
	        pdfFilePdf.Description = "PDF document"
	        portfolio.Files.Add(pdfFilePdf)
	
	        Dim pdfFileHtml As New PdfPortfolioFile(fileHtml)
	        pdfFileHtml.Description = "HTML document"
	        portfolio.Files.Add(pdfFileHtml)
	
	        Dim pdfFileImage As New PdfPortfolioFile(fileImage)
	        pdfFileImage.Description = "Image file"
	        portfolio.Files.Add(pdfFileImage)
	
	        ' set view mode
	        portfolio.ViewMode = PdfPortfolioViewMode.Details
	
	        ' set startup file
	        portfolio.StartupFileName = "logo.png"
	
	        ' save pdf document
	        portfolio.Save("Sample.pdf")
	
	        ' close pdf document
	        portfolio.Close()
```

The following sample code shows how to go through all embedded files from a PDF portfolio and export all of them to the disk:

```csharp
            // instantiate the pdf portfolio manager
            PdfPortfolioManager portfolio = new PdfPortfolioManager();

			// load existing PDF portfolio file
			portfolio.Load("portfolio.pdf");

			// go though the file list and export all files			
            for (int i = 0; i < portfolio.Files.Count; i++)
            {
                PdfPortfolioFile file = portfolio.Files[i];

                FileStream s = new FileStream("export\\" + file.Name, FileMode.Create);
                s.Write(file.Content, 0, file.Content.Length);
                s.Dispose();
            }

            // close pdf document
            portfolio.Close();
```

```vbnet
			' instantiate the pdf portfolio manager
			Dim portfolio As New PdfPortfolioManager()
			
			' load existing PDF portfolio file
			portfolio.Load("portfolio.pdf")
			
			' go though the file list and export all files			
			For i As Integer = 0 To portfolio.Files.Count - 1
				Dim file As PdfPortfolioFile = portfolio.Files(i)
			
				Dim s As New FileStream("export\" + file.Name, FileMode.Create)
				s.Write(file.Content, 0, file.Content.Length)
				s.Dispose()
			Next
			
			' close pdf document
			portfolio.Close()
```

## PDF compression

SelectPdf Library for .NET offers the possibility to reduce the size of newly generated or existing pdf documents using compression.

### Compress new PDF documents

Using SelectPdf Library for .NET, PDF compression can be enabled for newly created pdf documents.

**Compression Levels**

SelectPdf offers 3 compression levels that can be controlled using `PdfDocument.CompressionLevel` property of the `PdfDocument` object:

- NoCompression: PDF file is generated without any compression.
- Normal: PDF compression at normal speed with normal reduction of data size.
- Best: PDF best compression, but more time consuming.

### Sample Code

This sample code shows how to create a new PDF document using SelectPdf, how to add a text element to it and use compression to reduce the size of the generated pdf document.

```csharp
	// create a new pdf document
    PdfDocument doc = new PdfDocument();
    doc.Margins = new PdfMargins(20, 20, 20, 20);

    // set compression level
    doc.CompressionLevel = (PdfCompressionLevel)Enum.Parse(
        typeof(PdfCompressionLevel), compressionLevelValue, true);

    // create a new pdf font
    PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
    font.Size = 16;

    // add a new page to the document
    PdfPage page = doc.AddPage();

    // create a new text element and add it to the page
    PdfTextElement text = new PdfTextElement(0, 0, 
        Helper.SomeVeryLongText(), font);
    page.Add(text);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
        	' create a new pdf document
	        Dim doc As New PdfDocument()
	        doc.Margins = New PdfMargins(20, 20, 20, 20)
	
	        ' set compression level
	        doc.CompressionLevel = DirectCast([Enum].Parse(GetType(PdfCompressionLevel), _
	            compressionLevelValue, True), PdfCompressionLevel)
	
	        ' create a new pdf font
	        Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
	        font.Size = 16
	
	        ' add a new page to the document
	        Dim page As PdfPage = doc.AddPage()
	
	        ' create a new text element and add it to the page
	        Dim text As New PdfTextElement(0, 0, _
	            Helper.SomeVeryLongText(), font)
	        page.Add(text)
	
	        ' save pdf document
	        doc.Save("Sample.pdf")
	
	        ' close pdf document
	        doc.Close()
```

### Compress existing PDF documents

SelectPdf can also compress existing PDF documents. Load the document with the loaded-document constructor (`PdfDocument`), set the compression options and save - the compression is applied when the document is saved. This is built into the main library; no additional package is required.

Two independent compression axes control how the document is optimized:

- **Compress PDF streams and fonts** - the 3 compression levels of `PdfDocument.CompressionLevel` (`PdfCompressionLevel.NoCompression`, `PdfCompressionLevel.Normal`, `PdfCompressionLevel.Best`) control Flate/DEFLATE compression on content streams and fonts. `Best` additionally strips unused embedded font subsets.
- **Compress images from the PDF document** - controlled by `PdfDocument.JpegCompressionEnabled` and `PdfDocument.JpegCompressionLevel` (a value between 0 - best quality / least compression, and 100 - most compression). When enabled, images are re-encoded as JPEG.

### Sample Code

This sample code shows how to load an existing PDF document using SelectPdf and compress it to reduce the file size.

```csharp
// load existing file
string file = @"C:\Path\to\document.pdf";
PdfDocument doc = new PdfDocument(file);

// set compression options
doc.CompressionLevel = PdfCompressionLevel.Best;   // streams + fonts
doc.JpegCompressionEnabled = true;                 // re-encode images as JPEG
// 0 = best quality, 100 = most compression
doc.JpegCompressionLevel = 20;

// save compressed document (compression is applied on save)
doc.Save(file + "-compressed.pdf");

// get pdf document details
Console.WriteLine("Finished compressing PDF document with {0} pages.",
    doc.Pages.Count);
Console.WriteLine("Title: " + doc.DocumentInformation.Title);
Console.WriteLine("Author: " + doc.DocumentInformation.Author);
Console.WriteLine("Producer: " + doc.DocumentInformation.Producer);

doc.Close();
```

```vbnet
            ' load existing file
		    Dim file As String = "C:\Path\to\document.pdf"
		    Dim doc As PdfDocument = New PdfDocument(file)

            ' set compression options
		    doc.CompressionLevel = PdfCompressionLevel.Best   ' streams + fonts
		    doc.JpegCompressionEnabled = True                 ' re-encode images as JPEG
		    ' 0 = best quality, 100 = most compression
		    doc.JpegCompressionLevel = 20

            ' save compressed document (compression is applied on save)
		    doc.Save(file & "-compressed.pdf")

			' get pdf document details
		    Console.WriteLine("Finished compressing PDF document with {0} pages.",
		        doc.Pages.Count)
		    Console.WriteLine("Title: " & doc.DocumentInformation.Title)
		    Console.WriteLine("Author: " & doc.DocumentInformation.Author)
		    Console.WriteLine("Producer: " & doc.DocumentInformation.Producer)

		    doc.Close()
```

### Limit the resolution of images

Large photos are often drawn much smaller than their pixel size: a 3000 pixel wide photo shown 4 inches wide is 750 pixels per inch, where 150 is plenty on screen and 300 in print. Set `PdfDocument.ImagesMaxResolution` (pixels per inch) and every image drawn at a higher resolution is resampled down to it. The resolution is measured against the size the image is drawn at, so nothing changes on the page, and an image drawn in several places keeps enough pixels for the largest. The same setting exists on `HtmlToPdfOptions.ImagesMaxResolution` and `WordToPdfOptions.ImagesMaxResolution`.

On a document loaded from a file, the images of the whole document are resampled when it is saved; on a new document, the images added with `PdfImageElement` after the property is set. Gray and RGB images are resampled; image masks, indexed and CMYK images are left as they are, and a document that arrived signed is not resampled, since that would invalidate its signatures.

```csharp
PdfDocument doc = new PdfDocument("brochure.pdf");
doc.ImagesMaxResolution = 150;
doc.Save("brochure-small.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument("brochure.pdf")
doc.ImagesMaxResolution = 150
doc.Save("brochure-small.pdf")
doc.Close()
```
