# PDF to text, text search and PDF to image

Converted from the shipped SelectPdf.Universal v26.4 help topics. C# and VB.NET samples are the official ones.

## PDF to text

SelectPdf Library for .NET can be used to extract text from PDF using the **Pdf To Text Converter**.

The main class of the PDF to Text Converter is `PdfToText`. The PDF can be loaded using `PdfTool.Load(string)` methods. The text can be extracted from PDF as a plain text string using the `PdfToText.GetText` method.

Altenativelly, the text can be written directly into a file using `PdfToText.SaveText(string)`.

The `PdfToText` class provides are few other features:

- Get number of pages in the PDF document using the `PdfTool.GetPageCount` method.
- Get the information of the PDF document using the `PdfTool.GetInfo` method.

Starting with version 22.1 of SelectPdf, text can be extracted from specific coordinates in the PDF document using the method `PdfToText.ExtractText(int, double, double, double, double)`.

### Quick Start

Using the Pdf To Text Converter is very easy. The first thing that needs to be done is the namespace importing.

```csharp
			using SelectPdf.Universal;
```

```vbnet
			Imports SelectPdf.Universal
```

Sample code that shows how to extract the text from a PDF document using Select.Pdf Library for .NET:

```csharp
			// instantiate a pdf to text converter object
            PdfToText pdfToText = new PdfToText();

            // load PDF file
            pdfToText.Load(filePdf);

            // set the properties
            pdfToText.Layout = textLayout;
            pdfToText.StartPageNumber = startPage;
            pdfToText.EndPageNumber = endPage;

            // extract the text
            string text = pdfToText.GetText();
```

```vbnet
			' instantiate a pdf to text converter object
	        Dim pdfToText As New PdfToText()
	
	        ' load PDF file
	        pdfToText.Load(filePdf)
	
	        ' set the properties
	        pdfToText.Layout = textLayout
	        pdfToText.StartPageNumber = startPage
	        pdfToText.EndPageNumber = endPage
	
	        ' extract the text
	        Dim text As String = pdfToText.GetText()
```

### Pdf To Text Converter Properties

`PdfTool.StartPageNumber` - The page number from where the current operation will start on the PDF file. The default value is 1 which means that the operation will start from the first page.

`PdfTool.EndPageNumber` - The page number where the current operation will end on the PDF file. The default value is 0 which means that all the PDF document is processed starting from the `PdfTool.StartPageNumber` page.

`PdfTool.UserPassword` - The user password to be used to open the PDF document for reading. The default value is null, which means that no password will be used to open the PDF document.

`PdfToText.Layout` - Gets or sets the `TextLayout` of the output text. The default value is `TextLayout.Original`.

`PdfToText.ExtractText(int, double, double, double, double)` - Extracts the text from a rectangular region of a page of the PDF document: the page number (starting at 1), then x, y, width and height in points.

### Layout, Region Extraction, and Text Encoding

**Text layout** - controlled by `PdfToText.Layout`, type `TextLayout`:

- `TextLayout.Original` (default) preserves the spatial layout of the source PDF, padding the text so that columns stay aligned. Best for forms, invoices and tables where the visual column structure carries meaning.
- `TextLayout.Reading` produces the text in reading order, one run after another. Best for free-running prose or when feeding the output into a search index or natural-language pipeline.

**Plain text.** `PdfToText.GetText` returns a plain .NET `String` - the right choice for downstream processing (indexing, diffing, search).

**Encoding.** The strings returned by `GetText` are standard .NET UTF-16 strings. When persisting the result to a file or stream, pick a text encoding explicitly - for example `System.Text.Encoding.UTF8` - so the output round-trips correctly for non-ASCII content (the sample below demonstrates this).

**Region extraction.** Use `PdfToText.ExtractText(int, double, double, double, double)` to extract the text inside a rectangular region of a page (the page number, starting at 1, plus x, y, width and height in points).

### Pdf To Text Converter Sample

This sample shows how to use SelectPdf Pdf Library for .NET to extract text from a PDF document, also setting a few properties.

```csharp
			// the test file
            string filePdf = @"C:\Path\to\document.pdf";

            // settings
            string text_layout = textLayoutValue;
            TextLayout textLayout = (TextLayout)Enum.Parse(typeof(TextLayout),
                text_layout, true);

            int startPage = 1;
            try
            {
                startPage = Convert.ToInt32(startPageValue);
            }
            catch { }

            int endPage = 0;
            try
            {
                endPage = Convert.ToInt32(endPageValue);
            }
            catch { }

            // instantiate a pdf to text converter object
            PdfToText pdfToText = new PdfToText();

            // load PDF file
            pdfToText.Load(filePdf);

            // set the properties
            pdfToText.Layout = textLayout;
            pdfToText.StartPageNumber = startPage;
            pdfToText.EndPageNumber = endPage;

            // extract the text
            string text = pdfToText.GetText();

            // the extracted text can be written straight to a file as well,
            // without going through GetText - SaveText writes it as UTF-8
            pdfToText.SaveText("output.txt");

            // or as UTF-8 bytes, to be sent over the wire by the application
            byte[] utf8 = System.Text.Encoding.UTF8.GetBytes(text);
```

```vbnet
			' the test file
	        Dim filePdf As String = "C:\Path\to\document.pdf"
	
	        ' settings
	        Dim text_layout As String = textLayoutValue
	        Dim textLayout As TextLayout = _
	            DirectCast([Enum].Parse(GetType(TextLayout), text_layout, True),  _
	            TextLayout)
	
	        Dim startPage As Integer = 1
	        Try
	            startPage = Convert.ToInt32(startPageValue)
	        Catch
	        End Try
	
	        Dim endPage As Integer = 0
	        Try
	            endPage = Convert.ToInt32(endPageValue)
	        Catch
	        End Try
	
	        ' instantiate a pdf to text converter object
	        Dim pdfToText As New PdfToText()
	
	        ' load PDF file
	        pdfToText.Load(filePdf)
	
	        ' set the properties
	        pdfToText.Layout = textLayout
	        pdfToText.StartPageNumber = startPage
	        pdfToText.EndPageNumber = endPage
	
	        ' extract the text
	        Dim text As String = pdfToText.GetText()
	
	        ' the extracted text can be written straight to a file as well,
	        ' without going through GetText - SaveText writes it as UTF-8
	        pdfToText.SaveText("output.txt")

	        ' or as UTF-8 bytes, to be sent over the wire by the application
	        Dim utf8 As Byte() = System.Text.Encoding.UTF8.GetBytes(text)
```

## Searching text

The Pdf To Text Converter provided by SelectPdf, described in "PDF to text" (extract-text-image-search.md) section, provides one other very interesting feature: the possibility to search for text in a PDF document and retrieve the locations for all searched text occurrences.

This can be done using the `PdfToText.Search(string)` method of the `PdfToText` class. The method will return an array of `TextPosition` objects.

### Sample Code

This sample shows how to use SelectPdf PDF Library for .NET to search for text in a PDF document. A new PDF will be created highlighting the text that has been found.

```csharp
			// the test file
            string filePdf = @"C:\Path\to\document.pdf";

            // settings
            bool caseSensitive = false;
            bool wholeWordsOnly = false;

            // instantiate a pdf to text converter object
            PdfToText pdfToText = new PdfToText();

            // load PDF file
            pdfToText.Load(filePdf);

            // search for text and retrieve all found text positions
            TextPosition[] positions = pdfToText.Search(searchText, 
                caseSensitive, wholeWordsOnly);

            // open the existing PDF document in editing mode
            PdfDocument doc = new PdfDocument(filePdf);

            // highlight the found text in the existing PDF document
            for (int i = 0; i < positions.Length; i++)
            {
                TextPosition position = (TextPosition)positions[i];

                PdfPage page = doc.Pages[position.PageNumber - 1];

                PdfRectangleElement rect = new PdfRectangleElement(
                    position.X, position.Y, position.Width, position.Height);
                rect.BackColor = new PdfColor(240, 240, 0);
                rect.Transparency = 30;
                page.Add(rect);
            }

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
        	' the test file
	        Dim filePdf As String = "C:\Path\to\document.pdf"
	
	        ' settings
	        Dim caseSensitive As Boolean = False
	        Dim wholeWordsOnly As Boolean = False
	
	        ' instantiate a pdf to text converter object
	        Dim pdfToText As New PdfToText()
	
	        ' load PDF file
	        pdfToText.Load(filePdf)
	
	        ' search for text and retrieve all found text positions
	        Dim positions As TextPosition() = pdfToText.Search( _
	            searchText, caseSensitive, wholeWordsOnly)
	
	        ' open the existing PDF document in editing mode
	        Dim doc As New PdfDocument(filePdf)
	
	        ' highlight the found text in the existing PDF document
	        For i As Integer = 0 To positions.Length - 1
	            Dim position As TextPosition = DirectCast(positions(i), TextPosition)
	
	            Dim page As PdfPage = doc.Pages(position.PageNumber - 1)
	
	            Dim rect As New PdfRectangleElement( _
	                position.X, position.Y, position.Width, position.Height)
	            rect.BackColor = New PdfColor(240, 240, 0)
	            rect.Transparency = 30
	            page.Add(rect)
	        Next
	
	        ' save pdf document
	        doc.Save("Sample.pdf")
	
	        ' close pdf document
	        doc.Close()
```

## PDF to image

SelectPdf Library for .NET can be used to convert PDF pages to PNG, JPG, BMP or TIFF using the **Pdf To Image Converter**.

The main class of the PDF to Image Converter is `PdfRasterizer`. The PDF can be loaded using `PdfTool.Load(string)` methods. The PDF pages can be converted to sytem Image objects using the the `PdfRasterizer.ConvertToImages` merthod or to TIFF multiframe images using the `PdfRasterizer.ConvertToTiff` method.

Altenativelly, the images can be saved directly into files on disk using `PdfRasterizer.Save` or the `PdfRasterizer.SaveTiff` methods.

The `PdfRasterizer` class provides are few other features:

- Get number of pages in the PDF document using the `PdfTool.GetPageCount` method.
- Get the information of the PDF document using the `PdfTool.GetInfo` method.

### Pdf To Image Converter Properties

`PdfTool.StartPageNumber` - The page number from where the current operation will start on the PDF file. The default value is 1 which means that the operation will start from the first page.

`PdfTool.EndPageNumber` - The page number where the current operation will end on the PDF file. The default value is 0 which means that all the PDF document is processed starting from the `PdfTool.StartPageNumber` page.

`PdfTool.UserPassword` - The user password to be used to open the PDF document for reading. The default value is null, which means that no password will be used to open the PDF document.

`PdfRasterizer.ColorSpace` - The color space of the generated images. Default value is RGB.

`PdfRasterizer.Resolution` - The resolution of the generated images in DPI. Default value is 120.

Some additional properties can be set if the images are saved directly into files on disk using the `PdfRasterizer.Save` method:

`PdfRasterizer.ImagesFormat` - The image format for the generated images.

`PdfRasterizer.ImagesPath` - The path to the disk folder where the images will be saved.

`PdfRasterizer.ImagesPrefix` - The prefix for the generated images file name.

### Choosing Resolution, Format and Color Space

A few rules of thumb:

- **Resolution (DPI).** 72 DPI for preview thumbnails, 120 DPI (default) for general screen display, 150 DPI for decent print, 300 DPI for high-quality print. File size and memory scale quadratically with DPI - doubling the DPI quadruples the pixel count per page.
- **PNG.** Lossless, best for screenshots, diagrams, and text-heavy pages where sharp edges matter.
- **JPG.** Lossy; best for photo-dominated pages where smaller file size outweighs edge crispness.
- **TIFF.** Use `PdfRasterizer.ConvertToTiff` or `PdfRasterizer.SaveTiff` to bundle all selected pages in a single multi-frame file.
- **BMP.** Uncompressed; file sizes are large. Mostly for legacy pipelines that require raw pixels.
- **Color space.** `PdfRasterizerColorSpace.RGB` (default) is full color. `PdfRasterizerColorSpace.Gray` produces grayscale images that are roughly one-third the size of RGB equivalents - useful when the source is monochrome (scanned text, line drawings) or when color is not needed.

### Pdf To Image Converter Sample

This sample shows how to use SelectPdf Pdf Library for .NET to convert PDF document pages to different image formats.

```csharp
// the test file
string filePdf = "selectpdf.pdf";

// instantiate a pdf rasterizer (pdf to image converter) object
PdfRasterizer rasterizer = new PdfRasterizer();

// load PDF file
rasterizer.Load(filePdf);

// set the page range (1-based; EndPageNumber = 0 means up to the last page)
rasterizer.StartPageNumber = 1;
rasterizer.EndPageNumber = 0;

// other properties that can be set
rasterizer.Resolution = 150;
rasterizer.ColorSpace = PdfRasterizerColorSpace.RGB;

// option 1: get the rasterized pages as PNG byte arrays (one per page)
byte[][] images = rasterizer.ConvertToImages();
for (int i = 0; i < images.Length; i++)
{
    System.IO.File.WriteAllBytes("page-" + (i + 1) + ".png", images[i]);
}

// option 2: save every selected page to a file, choosing the format
rasterizer.ImagesPath = ".";
rasterizer.ImagesPrefix = "page";
rasterizer.ImagesFormat = PdfImageFormat.Jpeg;   // Png, Jpeg or Bmp
rasterizer.Save();

// option 3: bundle all selected pages into a single multi-frame TIFF
byte[] tiff = rasterizer.ConvertToTiff();
System.IO.File.WriteAllBytes("pages.tiff", tiff);
```

```vbnet
' the test file
Dim filePdf As String = "selectpdf.pdf"

' instantiate a pdf rasterizer (pdf to image converter) object
Dim rasterizer As New PdfRasterizer()

' load PDF file
rasterizer.Load(filePdf)

' set the page range (1-based; EndPageNumber = 0 means up to the last page)
rasterizer.StartPageNumber = 1
rasterizer.EndPageNumber = 0

' other properties that can be set
rasterizer.Resolution = 150
rasterizer.ColorSpace = PdfRasterizerColorSpace.RGB

' option 1: get the rasterized pages as PNG byte arrays (one per page)
Dim images As Byte()() = rasterizer.ConvertToImages()
For i As Integer = 0 To images.Length - 1
    System.IO.File.WriteAllBytes("page-" & (i + 1) & ".png", images(i))
Next

' option 2: save every selected page to a file, choosing the format
rasterizer.ImagesPath = "."
rasterizer.ImagesPrefix = "page"
rasterizer.ImagesFormat = PdfImageFormat.Jpeg   ' Png, Jpeg or Bmp
rasterizer.Save()

' option 3: bundle all selected pages into a single multi-frame TIFF
Dim tiff As Byte() = rasterizer.ConvertToTiff()
System.IO.File.WriteAllBytes("pages.tiff", tiff)
```
