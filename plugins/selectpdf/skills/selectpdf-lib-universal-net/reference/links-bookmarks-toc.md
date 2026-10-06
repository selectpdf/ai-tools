# Links, bookmarks, table of contents and open actions

Converted from the shipped SelectPdf.Universal v26.4 help topics. C# and VB.NET samples are the official ones.

## Links in HTML to PDF

Select.Pdf Library can convert internal html links to internal pdf links and keep external html links (hyperlinks to other pages) the same in pdf (external links). These links can be disabled in pdf if needed.

There are 2 properties that can be used to enable and disable internal and external links:

- `HtmlToPdfOptions.InternalLinksEnabled` - Controls the conversion of internal html links to internal pdf links. The default value for this property is *True*.
- `HtmlToPdfOptions.ExternalLinksEnabled` - Controls the rendering of external hyperlinks in pdf. The default value for this property is *True*.

### Sample Code

This sample code shows how the html to pdf converter can handle internal and external links from the web page when converted to pdf using Select.Pdf Pdf Library for .NET.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set links options
    converter.Options.InternalLinksEnabled = true;
    converter.Options.ExternalLinksEnabled = true;

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

    ' set links options
    converter.Options.InternalLinksEnabled = True
    converter.Options.ExternalLinksEnabled = True

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Automatic bookmarks

As described in "Bookmarks" (links-bookmarks-toc.md) section, Select.Pdf Library for .NET offers support for pdf bookmarks. The html to pdf converter offers additional features and can automatically generate pdf bookmarks based on some html elements selection.

The elements that will be bookmarked are defined using the `HtmlToPdfOptions.PdfBookmarkOptions` property of the `HtmlToPdfOptions` object. This is an instance of the `PdfBookmarkOptions` class, with two properties: `PdfBookmarkOptions.CssSelectors`, which selects the elements, and `PdfBookmarkOptions.HierarchicalBookmarks`, which decides how the bookmarks are arranged.

In other words, the elements that will be bookmarked are defined using CSS selectors. For example, the selector for all the H1 elements is "H1", the selector for all the elements with the CSS class name 'myclass' is "*.myclass" and the selector for the elements with the id 'myid' is "*#myid". Read more about CSS selectors [here](http://www.w3schools.com/cssref/css_selectors.asp).

By default every bookmark is a top-level entry of the outline. Set `PdfBookmarkOptions.HierarchicalBookmarks` to true to nest them in the order of the selectors instead: elements matched by the first selector become top-level entries, elements matched by the second selector are nested under the entry that precedes them, and so on. With the selectors "H1", "H2" each H2 bookmark appears under the H1 before it. A heading that appears before any heading of the level above it is still placed in a valid tree, one level below the deepest entry open at that point.

### Sample Code

This sample code shows how the html to pdf converter can automatically generate pdf bookmarks based on H1 and H2 elements from the web page using Select.Pdf Pdf Library for .NET.

```csharp
	// instantiate a html to pdf converter object
    HtmlToPdf converter = new HtmlToPdf();

    // set the css selectors for the automatic bookmarks
    converter.Options.PdfBookmarkOptions.CssSelectors = new string[] {"H1", "H2"};

    // nest the H2 bookmarks under the H1 bookmark that precedes them
    converter.Options.PdfBookmarkOptions.HierarchicalBookmarks = true;

    // display the bookmarks when the document is opened in a viewer
    converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines;

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

    ' set the css selectors for the automatic bookmarks
    converter.Options.PdfBookmarkOptions.CssSelectors = New String() {"H1", "H2"}

    ' nest the H2 bookmarks under the H1 bookmark that precedes them
    converter.Options.PdfBookmarkOptions.HierarchicalBookmarks = True

    ' display the bookmarks when the document is opened in a viewer
    converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines

    ' create a new pdf document converting an url
    Dim doc As PdfDocument = converter.ConvertUrl(url)

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Table of contents

SelectPdf Html To Pdf Converter generates easily internal links ("Links in HTML to PDF" (links-bookmarks-toc.md)) and bookmarks ("Automatic bookmarks" (links-bookmarks-toc.md)). Using these 2 features, a simple table of contents is automatically generated if the page being converted has a section at the top with internal links to all major sections.

A little more complicated is to generate page numbers for the table of contents. To be able to do that, the "Web element locations" (web-elements-and-auth.md) feature will be used together with a few more settings and web element IDs patterns.

The table of contents (TOC) must already exist at the top of the web page and the items from the table of contents must link to the appropriate sections from the document (like in the test document below). The TOC elements must also have the following CSS classes or ID styles:

- All TOC elements (both titles from the actual table of contents, but also the link targets - where the user goes when clicking on a TOC link) must have a specific CSS class ("toc" in our example).
- All TOC titles must have similar IDs: TOC_Title{0} (TOC_Title1 to TOC_Title13 in our example).
- All TOC targets (document content) must have similar IDs: TOC_Target{0} (TOC_Target1 to TOC_Target13 in our example).

All these elements locations are retrieved during the conversion to PDF and those locations are used to get the content page numbers to display them in the table of contents. The font for the page numbers can be specified in code. Also, that style of the line that links the TOC titles with the page numbers.

### Sample Code

This sample shows how the html to pdf converter can generate page numbers for a table of contents using SelectPdf Pdf Library for .NET.

```csharp
        	// number of items in the table of contents (TOC)
	        int TOC_Items = 13;
	        
	        // CSS class for TOC items (titles and targets)
	        string TOC_Class = "toc";
	
	        // font size for page numbers in the table of contents
	        int TOC_Page_Font_Size = 12;
	
	        // CSS class for PDF bookmarks
	        string Bookmarks_Class = "bookmark";
	
	        // Each TOC item is represented by a title with ID TOC_Title{0}
	        // and a target (internal link) with ID TOC_Target{0}
	        // Both TOC titles and targets must have the same CSS Class ("toc" in our sample)
	
            // the url of the document that will be converted
            string url = "https://www.example.com/document2.html";

            // instantiate a html to pdf converter object
            HtmlToPdf converter = new HtmlToPdf();

            converter.Options.MarginTop = 20;
            converter.Options.MarginRight = 20;
            converter.Options.MarginBottom = 20;
            converter.Options.MarginLeft = 20;

            // set the css selectors for the automatic bookmarks 
            // (elements with CSS class "bookmark")
            converter.Options.PdfBookmarkOptions.CssSelectors =
                new string[] { "*." + Bookmarks_Class };

            // set the css selectors for the TOC related elements
            // (elements with CSS class "toc")
            // (alternatively, full list of IDs can be specified)
            converter.Options.WebElementsMappingOptions.CssSelectors =
                new string[] { "*." + TOC_Class };

            // display the bookmarks when the document is opened in a viewer
            converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines;

            // footer settings
            converter.Options.DisplayFooter = true;
            converter.Footer.Height = 30;

            // page numbers can be added using a PdfTextSection object, using a system font
            // (subset-embedded). Liberation Sans resolves on any host: the library bundles it
            // and serves it from embedded bytes when it is not installed.
            PdfFont footerFont = PdfFont.CreateFromSystemFont("Liberation Sans", 10f);
            PdfTextSection text = new PdfTextSection(0, 10,
                "Page: {page_number} of {total_pages}  ",
                footerFont);
            text.HorizontalAlign = PdfTextHorizontalAlign.Right;
            converter.Footer.Add(text);


            // create a new pdf document converting an url
            PdfDocument doc = converter.ConvertUrl(url);

            // create font for page numbers
            PdfFont pageNumberFont = doc.Fonts.Add(
                PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, TOC_Page_Font_Size));

            // get the right coordinate of the table of contents 
            // to position the page numbers
            float tocRight = doc.Pages[0].ClientRectangle.Width - 50;

            // add page numbers for the table of contents items
            for (int tocItem = 1; tocItem <= TOC_Items; tocItem++)
            {
                string tocTitleID = String.Format("TOC_Title{0}", tocItem);
                string tocTargetID = String.Format("TOC_Target{0}", tocItem);

                WebElement tocTitle = converter.Options.WebElementsMappingOptions
                    .Result.GetElementByHtmlId(tocTitleID);
                WebElement tocTarget = converter.Options.WebElementsMappingOptions
                    .Result.GetElementByHtmlId(tocTargetID);

                if (tocTitle == null || tocTarget == null)
                {
                    // TOC items not found
                    continue;
                }

                // get the TOC title page and rendering rectangle
                PdfPage tocPage = doc.Pages[tocTitle.PdfRectangles[0].PageIndex];
                RectangleF tocTitleRectangle = tocTitle.PdfRectangles[0].Rectangle;

                // get the page number of target where the TOC entry points
                int tocTargetPageNumber = tocTarget.PdfRectangles[0].PageIndex + 1;

                // add dashed line from TOC title to the page number
                PdfLineElement dashedLine = new PdfLineElement(
                    tocTitleRectangle.Right + 5, 
                    tocTitleRectangle.Y + tocTitleRectangle.Height - 4,
                    tocRight, tocTitleRectangle.Y + tocTitleRectangle.Height - 4);
                dashedLine.LineStyle.LineWidth = 1;
                dashedLine.LineStyle.LineDashStyle = PdfLineDashStyle.Dash;
                dashedLine.ForeColor = Color.Black;
                tocPage.Add(dashedLine);

                // create the page number text element to the right of the TOC title
                PdfTextElement pageNumberTextElement = new PdfTextElement(tocRight + 5, 
                        tocTitleRectangle.Y, -1, tocTitleRectangle.Height,
                        tocTargetPageNumber.ToString(), pageNumberFont);
                pageNumberTextElement.HorizontalAlign = PdfTextHorizontalAlign.Left;
                pageNumberTextElement.VerticalAlign = PdfTextVerticalAlign.Middle;
                pageNumberTextElement.ForeColor = Color.Black;

                // add the page number to the right of the TOC entry
                tocPage.Add(pageNumberTextElement);
            }

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
        	' number of items in the table of contents (TOC)
		    Dim TOC_Items As Integer = 13
		
		    ' CSS class for TOC items (titles and targets)
		    Dim TOC_Class As String = "toc"
		
		    ' font size for page numbers in the table of contents
		    Dim TOC_Page_Font_Size As Integer = 12
		
		    ' CSS class for PDF bookmarks
		    Dim Bookmarks_Class As String = "bookmark"
		
		    ' Each TOC item is represented by a title with ID TOC_Title{0}
		    ' and a target (internal link) with ID TOC_Target{0}
		    ' Both TOC titles and targets must have the same CSS Class ("toc" in our sample)
		
	        ' the url of the document that will be converted
	        Dim url As String = "https://www.example.com/document2.html"

	        ' instantiate a html to pdf converter object
	        Dim converter As New HtmlToPdf()
	
	        converter.Options.MarginTop = 20
	        converter.Options.MarginRight = 20
	        converter.Options.MarginBottom = 20
	        converter.Options.MarginLeft = 20
	
	        ' set the css selectors for the automatic bookmarks 
	        ' (elements with CSS class "bookmark")
	        converter.Options.PdfBookmarkOptions.CssSelectors = _
	            New String() {Convert.ToString("*.") & Bookmarks_Class}
	
	        ' set the css selectors for the TOC related elements
	        ' (elements with CSS class "toc")
	        ' (alternatively, full list of IDs can be specified)
	        converter.Options.WebElementsMappingOptions.CssSelectors = _
	            New String() {Convert.ToString("*.") & TOC_Class}
	
	        ' display the bookmarks when the document is opened in a viewer
	        converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines
	
	        ' footer settings
	        converter.Options.DisplayFooter = True
	        converter.Footer.Height = 30
	
	        ' page numbers can be added using a PdfTextSection object
	        ' Liberation Sans resolves on any host: the library bundles it and serves it
	        ' from embedded bytes when it is not installed.
	        Dim footerFont As PdfFont = PdfFont.CreateFromSystemFont("Liberation Sans", 10F)
	        Dim text As New PdfTextSection(0, 10, "Page: {page_number} of {total_pages}  ", _
	                                       footerFont)
	        text.HorizontalAlign = PdfTextHorizontalAlign.Right
	        converter.Footer.Add(text)
	
	
	        ' create a new pdf document converting an url
	        Dim doc As PdfDocument = converter.ConvertUrl(url)
	
	        ' create font for page numbers
	        Dim pageNumberFont As PdfFont = doc.Fonts.Add( _
	            PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, TOC_Page_Font_Size))
	
	        ' get the right coordinate of the table of contents 
	        ' to position the page numbers
	        Dim tocRight As Single = doc.Pages(0).ClientRectangle.Width - 50
	
	        ' add page numbers for the table of contents items
	        For tocItem As Integer = 1 To TOC_Items
	            Dim tocTitleID As String = [String].Format("TOC_Title{0}", tocItem)
	            Dim tocTargetID As String = [String].Format("TOC_Target{0}", tocItem)
	
	            Dim tocTitle As WebElement = converter.Options.WebElementsMappingOptions. _
	                Result.GetElementByHtmlId(tocTitleID)
	            Dim tocTarget As WebElement = converter.Options.WebElementsMappingOptions. _
	                Result.GetElementByHtmlId(tocTargetID)
	
	            If tocTitle Is Nothing OrElse tocTarget Is Nothing Then
	                ' TOC items not found
	                Continue For
	            End If
	
	            ' get the TOC title page and rendering rectangle
	            Dim tocPage As PdfPage = doc.Pages(tocTitle.PdfRectangles(0).PageIndex)
	            Dim tocTitleRectangle As RectangleF = tocTitle.PdfRectangles(0).Rectangle
	
	            ' get the page number of target where the TOC entry points
	            Dim tocTargetPageNumber As Integer = tocTarget.PdfRectangles(0).PageIndex + 1
	
	            ' add dashed line from TOC title to the page number
	            Dim dashedLine As New PdfLineElement(tocTitleRectangle.Right + 5, _
	                                    tocTitleRectangle.Y + tocTitleRectangle.Height - 4, _
	                                    tocRight, _
	                                    tocTitleRectangle.Y + tocTitleRectangle.Height - 4)
	            dashedLine.LineStyle.LineWidth = 1
	            dashedLine.LineStyle.LineDashStyle = PdfLineDashStyle.Dash
	            dashedLine.ForeColor = Color.Black
	            tocPage.Add(dashedLine)
	
	            ' create the page number text element to the right of the TOC title
	            Dim pageNumberTextElement As New PdfTextElement(tocRight + 5, _
	                                    tocTitleRectangle.Y, _
	                                    -1, tocTitleRectangle.Height, _
	                                    tocTargetPageNumber.ToString(), _
	                                    pageNumberFont)
	            pageNumberTextElement.HorizontalAlign = PdfTextHorizontalAlign.Left
	            pageNumberTextElement.VerticalAlign = PdfTextVerticalAlign.Middle
	            pageNumberTextElement.ForeColor = Color.Black
	
	            ' add the page number to the right of the TOC entry
	            tocPage.Add(pageNumberTextElement)
	        Next
	
	        ' save pdf document
	        doc.Save("Sample.pdf")
	
	        ' close pdf document
	        doc.Close()
```

## Links

Select.Pdf offers support to create clickable links in a pdf document. A link can be used to navigate to another location within the same pdf document, open a web url or a local file. Here are the objects that can be used to create links:

- `PdfInternalLinkElement` - can be used to create a link that will help you navigate to another location within the current pdf document.
- `PdfExternalLinkElement` - can be used to create a link that will open an external web url.
- `PdfExternalFileElement` - can be used to create a link that will open a local file from the disk.

All these elements use a common property (**LinkRectangle**) to define the clickable area of the link. This area can be placed over a piece of text, image or any other pdf element. If that specific element was previously added to the pdf page, the rendering result object can be used to obtain its exact rendering rectangle specified by the `PdfRenderingResult.PdfPageLastRectangle` property of the `PdfRenderingResult` object.

`PdfInternalLinkElement` defines the `PdfInternalLinkElement.LinkDestination` property to specify the location that will be displayed when the link is clicked.

`PdfExternalLinkElement` defines the `PdfExternalLinkElement.LinkURL` property to specify the external url that will be opened when the link is clicked.

`PdfExternalFileElement` defines the `PdfExternalFileElement.FileName` property to specify the external file that will be opened when the link is clicked.

### Sample Code

This sample code shows how to create a new PDF document using Select.Pdf, how to add 2 pages to the document and several internal and external links:

```csharp
	        // create a new pdf document
            PdfDocument doc = new PdfDocument();

            // add a new page to the document
            PdfPage page1 = doc.AddPage();

            // add a second page to the document
            PdfPage page2 = doc.AddPage();

            // define a rendering result object
            PdfRenderingResult result;

            // create a new pdf font
            PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
            font.Size = 14;
            font.IsUnderline = true;

            // create a new text element and add it to the first page
            PdfTextElement text1 = new PdfTextElement(0, 0, 
                "First page (no link on this text)", font);
            page1.Add(text1);

            // create a new text element and add it to the second page
            PdfTextElement text2 = new PdfTextElement(0, 0, 
                "Second page (no link on this text)", font);
            page2.Add(text2);

            // create external link in 2 steps
            
            // 1 - create the link text
            PdfTextElement linkText1 = new PdfTextElement(0, 50, 
                "External link (click to go to selectpdf.com)", font);
            result = page1.Add(linkText1);
            
            // 2 - add the link using the text rendering rectangle
            PdfExternalLinkElement extLink1 = new PdfExternalLinkElement(
                result.PdfPageLastRectangle, "http://selectpdf.com");
            page1.Add(extLink1);

            // create internal link in 2 steps

            // 1 - create the link text
            PdfTextElement linkText2 = new PdfTextElement(0, 100, 
                "Internal link (click to go to the second page)", font);
            result = page1.Add(linkText2);

            // 2 - add the link using the text rendering rectangle
            PdfInternalLinkElement intLink1 = new PdfInternalLinkElement(
                result.PdfPageLastRectangle, new PdfDestination(page2));
            page1.Add(intLink1);

            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
	        ' create a new pdf document
            Dim doc As New PdfDocument()

            ' add a new page to the document
            Dim page1 As PdfPage = doc.AddPage()

            ' add a second page to the document
            Dim page2 As PdfPage = doc.AddPage()

            ' define a rendering result object
            Dim result As PdfRenderingResult

            ' create a new pdf font
            Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
            font.Size = 14
            font.IsUnderline = True

            ' create a new text element and add it to the first page
            Dim text1 As New PdfTextElement(0, 0, "First page (no link on this text)", font)
            page1.Add(text1)

            ' create a new text element and add it to the second page
            Dim text2 As New PdfTextElement(0, 0, "Second page (no link on this text)", font)
            page2.Add(text2)

            ' create external link in 2 steps

            ' 1 - create the link text
            Dim linkText1 As New PdfTextElement(0, 50, _
                    "External link (click to go to selectpdf.com)", font)
            result = page1.Add(linkText1)

            ' 2 - add the link using the text rendering rectangle
            Dim extLink1 As New PdfExternalLinkElement(result.PdfPageLastRectangle, _
                    "http://selectpdf.com")
            page1.Add(extLink1)

            ' create internal link in 2 steps

            ' 1 - create the link text
            Dim linkText2 As New PdfTextElement(0, 100, _
                    "Internal link (click to go to the second page)", font)
            result = page1.Add(linkText2)

            ' 2 - add the link using the text rendering rectangle
            Dim intLink1 As New PdfInternalLinkElement( _
                result.PdfPageLastRectangle, New PdfDestination(page2))
            page1.Add(intLink1)

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

## Bookmarks

Large documents are difficult to navigate. To make the navigation and content finding easier in a pdf document, pdf viewers can display a document outline associated with the pdf document. The document outline consists of a tree-structured hierarchy of bookmarks. This bookmarks tree serves as a table of contents for the pdf document, displaying its entire structure. When a bookmark item is clicked, the destination associated with it is displayed in the viewer.

Select.Pdf offers support for bookmarks creation and manipulation. A bookmark is handled by the `PdfBookmark` class. `PdfDocument` object handles the collection of bookmarks associated with it through the `PdfDocument.Bookmarks` property and the corresponding `PdfBookmarksCollection` class.

A bookmark can be added to a pdf document using one of the following methods:

- `PdfDocument.AddBookmark(string, PdfDestination)` - can be used to create a top level bookmark in the pdf document. When the bookmark is clicked, the pdf viewer will jump to the destination specified in second parameter of the method.
- `PdfDocument.AddBookmark(string, PdfDestination, PdfBookmark)` - can be used to create a bookmark in the pdf document that has another existing bookmark as parent. When the bookmark is clicked, the pdf viewer will jump to the destination specified in second parameter of the method.

The bookmarks hierarchy can be changed using the `PdfBookmark.Parent` and `PdfBookmark.ChildNodes` properties of the `PdfBookmark` object.

Bookmarks can be removed using the `PdfDocument.Bookmarks` collection of the `PdfDocument` object.

Bookmarks have styles and colors that can be specified using the `PdfBookmark.Style` and `PdfBookmark.Color` properties of the `PdfBookmark` object.

### Sample Code

This sample code shows how to create a new PDF document using Select.Pdf, how to add several pages, texts and bookmarks to the document.

```csharp
	// create a new pdf document
    PdfDocument doc = new PdfDocument();

    // instruct the document to show the bookmarks panel when opened in a viewer
    doc.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines;

    // add a new page to the document
    PdfPage page1 = doc.AddPage();

    // add a second page to the document
    PdfPage page2 = doc.AddPage();

    // add a third page to the document
    PdfPage page3 = doc.AddPage();

    // define a rendering result object
    PdfRenderingResult result;

    // create a new pdf font
    PdfFont font = doc.Fonts.Add(PdfStandardFont.Helvetica);
    font.Size = 14;

    // create a new text element and add it to the first page
    PdfTextElement text1 = new PdfTextElement(0, 0, "First page", font);
    page1.Add(text1);

    // create a new text element and add it to the second page
    PdfTextElement text2 = new PdfTextElement(0, 0, "Second page", font);
    page2.Add(text2);

    // create a new text element and add it to the third page
    PdfTextElement text3 = new PdfTextElement(0, 0, "Third page", font);
    page3.Add(text3);

    // create a first level bookmark pointing to the first page
    PdfBookmark book1 = doc.AddBookmark("First page", new PdfDestination(page1, 
        new SelectPdf.Universal.Drawing.PointF(0, 0)));

    // create a first level bookmark pointing to the second page
    PdfBookmark book2 = doc.AddBookmark("Second page", new PdfDestination(page2, 
        new SelectPdf.Universal.Drawing.PointF(0, 0)));

    // create a first level bookmark pointing to the third page
    PdfBookmark book3 = doc.AddBookmark("Third page", new PdfDestination(page3, 
        new SelectPdf.Universal.Drawing.PointF(0, 0)));

    // other text on page 2
    PdfTextElement text21 = new PdfTextElement(0, 100, "Text 1 on page 2", font);
    result = page2.Add(text21);

    // add a level 2 bookmark to this text 
    // (using the bookmark for the page 2 as parent and 
    // the text rendering location as bookmark destination)
    doc.AddBookmark("Text 1", new PdfDestination(page2, 
        result.PdfPageLastRectangle.Location), book2);

    // other text on page 2
    PdfTextElement text22 = new PdfTextElement(0, 400, "Text 2 on page 2", font);
    result = page2.Add(text22);

    // add a level 2 bookmark to this text 
    // (using the bookmark for the page 2 as parent and the text rendering 
    // location as bookmark destination) some styles are set for this bookmark
    PdfBookmark book = doc.AddBookmark("Text 2", new PdfDestination(page2, 
        result.PdfPageLastRectangle.Location), book2);
    book.Color = new PdfColor(255, 0, 0);
    book.Style = PdfBookmarkStyle.Italic;

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
	' create a new pdf document
    Dim doc As New PdfDocument()

    ' instruct the document to show the bookmarks panel when opened in a pdf viewer
    doc.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines

    ' add a new page to the document
    Dim page1 As PdfPage = doc.AddPage()

    ' add a second page to the document
    Dim page2 As PdfPage = doc.AddPage()

    ' add a third page to the document
    Dim page3 As PdfPage = doc.AddPage()

    ' define a rendering result object
    Dim result As PdfRenderingResult

    ' create a new pdf font
    Dim font As PdfFont = doc.Fonts.Add(PdfStandardFont.Helvetica)
    font.Size = 14

    ' create a new text element and add it to the first page
    Dim text1 As New PdfTextElement(0, 0, "First page", font)
    page1.Add(text1)

    ' create a new text element and add it to the second page
    Dim text2 As New PdfTextElement(0, 0, "Second page", font)
    page2.Add(text2)

    ' create a new text element and add it to the third page
    Dim text3 As New PdfTextElement(0, 0, "Third page", font)
    page3.Add(text3)

    ' create a first level bookmark pointing to the first page
    Dim book1 As PdfBookmark = doc.AddBookmark("First page", _
                New PdfDestination(page1, New SelectPdf.Universal.Drawing.PointF(0, 0)))

    ' create a first level bookmark pointing to the second page
    Dim book2 As PdfBookmark = doc.AddBookmark("Second page", _
                New PdfDestination(page2, New SelectPdf.Universal.Drawing.PointF(0, 0)))

    ' create a first level bookmark pointing to the third page
    Dim book3 As PdfBookmark = doc.AddBookmark("Third page", _
                New PdfDestination(page3, New SelectPdf.Universal.Drawing.PointF(0, 0)))

    ' other text on page 2
    Dim text21 As New PdfTextElement(0, 100, "Text 1 on page 2", font)
    result = page2.Add(text21)

    ' add a level 2 bookmark to this text 
    ' (using the bookmark for the page 2 as parent and 
    ' the text rendering location as bookmark destination)
    doc.AddBookmark("Text 1", New PdfDestination(page2, _
                                result.PdfPageLastRectangle.Location), book2)

    ' other text on page 2
    Dim text22 As New PdfTextElement(0, 400, "Text 2 on page 2", font)
    result = page2.Add(text22)

    ' add a level 2 bookmark to this text 
    ' (using the bookmark for the page 2 as parent and 
    ' the text rendering location as bookmark destination)
    ' some styles are set for this bookmark
    Dim book As PdfBookmark = doc.AddBookmark("Text 2", _
            New PdfDestination(page2, result.PdfPageLastRectangle.Location), book2)
    book.Color = New PdfColor(255, 0, 0)
    book.Style = PdfBookmarkStyle.Italic

    ' save pdf document
    doc.Save("Sample.pdf")

    ' close pdf document
    doc.Close()
```

## Open actions

Select.Pdf Library for .NET can create pdf documents that will perform a certain action when the document is opened in a pdf viewer.

To set a pdf document open action, the `PdfDocument.OpenAction` property of the `PdfDocument` object must be used. This is an instance of `PdfDocumentOpenAction` class that exposes the `PdfDocumentOpenAction.Action` property.

The pdf document can perform 2 types of actions when it is opened:

- `PdfActionGoTo` - The pdf document opens at a specific `PdfDestination` passed to the `PdfActionGoTo.ctor(PdfDestination)` constructor.
- `PdfActionJavaScript` - The pdf document will run a specific javascript.

### GoTo Action

To instruct the pdf viewer to open the pdf document at a certain page, location on that page and zoom factor, the `PdfDocumentOpenAction.Action` property needs to be set to an instance of `PdfActionGoTo` class.

The following sample code shows how to generate a pdf document from a html to pdf conversion and open the pdf document on page 2 directly in a pdf viewer.

```csharp
			// instantiate a html to pdf converter object
            HtmlToPdf converter = new HtmlToPdf();

            // create a new pdf document converting an url
            PdfDocument doc = converter.ConvertUrl(url);

			// set the second page as open action destination
            if (doc.Pages.Count > 1)
            {
                PdfPage secondPage = doc.Pages[1];
                PdfDestination secondPageDestination = new PdfDestination(secondPage);
                doc.OpenAction.Action = new PdfActionGoTo(secondPageDestination);
            }
			
            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' instantiate a html to pdf converter object
            Dim converter As New HtmlToPdf()

            ' create a new pdf document converting an url
            Dim doc As PdfDocument = converter.ConvertUrl(url)

			' set the second page as open action destination
			If doc.Pages.Count > 1 Then
				Dim secondPage As PdfPage = doc.Pages(1)
				Dim secondPageDestination As New PdfDestination(secondPage)
				doc.OpenAction.Action = New PdfActionGoTo(secondPageDestination)
			End If

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

### Javascript Action

To instruct the pdf viewer to execute a Javascript when the pdf document is opened in a pdf viewer, the `PdfDocumentOpenAction.Action` property needs to be set to an instance of `PdfActionJavaScript` class.

The following sample code shows how to generate a pdf document from a html to pdf conversion and display the print dialog when the pdf file is opened in a viewer.

```csharp
			// instantiate a html to pdf converter object
            HtmlToPdf converter = new HtmlToPdf();

            // create a new pdf document converting an url
            PdfDocument doc = converter.ConvertUrl(url);

			// set the javascript open action
			doc.OpenAction.Action = new PdfActionJavaScript("print()");
			
            // save pdf document
            doc.Save("Sample.pdf");

            // close pdf document
            doc.Close();
```

```vbnet
			' instantiate a html to pdf converter object
            Dim converter As New HtmlToPdf()

            ' create a new pdf document converting an url
            Dim doc As PdfDocument = converter.ConvertUrl(url)

			' set the javascript open action
			doc.OpenAction.Action = New PdfActionJavaScript("print()")

            ' save pdf document
            doc.Save("Sample.pdf")

            ' close pdf document
            doc.Close()
```

Here are some common JavaScript methods or properties supported by Adobe Acrobat Javascript. These should server as a parameter for `PdfActionJavaScript`.

The following javascript can be used to display an alert popup when the pdf document is displayed in a pdf viewer:

```javascript
			app.alert("Alert Message")
```

The following javascript can be used to open the print dialog when the pdf document is displayed in a pdf viewer:

```javascript
			print()
```

The following javascript can be used to print silently (viewer should only ask for permission) when the pdf document is displayed in a pdf viewer:

```javascript
			print({bUI: false, bSilent: true, bShrinkToFit: true})
```

The following javascript can be used to set the zoom to a certain level when the pdf document is displayed in a pdf viewer:

```javascript
			zoom = 150
```
