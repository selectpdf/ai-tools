# Links, bookmarks, table of contents and open actions

How the commercial Select.Pdf .NET library preserves HTML links during conversion and adds links, bookmarks, a page-numbered table of contents, and viewer open actions to a `PdfDocument`.

## Keeping HTML links in the PDF during conversion

`HtmlToPdfConverter` carries links from the web page into the PDF via two `HtmlToPdfOptions` properties: `InternalLinksEnabled` converts in-page HTML links into internal PDF links, and `ExternalLinksEnabled` keeps hyperlinks to other pages as external PDF links. Both default to `true`; set either to `false` to drop those links from the output.

With the Chromium and Blink engines (v26.5), an internal-link destination whose name is longer than 127 bytes - an anchor in a Japanese heading, for example - is renamed `spdfdest1`, `spdfdest2`, ... and the links to it are updated, because PDF/A does not allow longer names. A link to a destination the page does not define (a "skip to content" link with an empty target, or an anchor that is not on the page) keeps its area but no longer points anywhere.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set links options
converter.Options.InternalLinksEnabled = true;
converter.Options.ExternalLinksEnabled = true;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);

// save pdf document
doc.Save("output.pdf");
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
doc.Save("output.pdf")
doc.Close()
```

## Adding links manually to a PdfDocument

Three element classes create clickable links: `PdfInternalLinkElement` (jumps to a location in the same document, via its `LinkDestination` property), `PdfExternalLinkElement` (opens a web URL, via `LinkURL`), and `PdfExternalFileElement` (opens a local file, via `FileName`). All define the clickable area through a `LinkRectangle`; place that rectangle over previously added content by reusing the `PdfRenderingResult.PdfPageLastRectangle` returned from `PdfPage.Add(...)`. An internal link targets a `PdfDestination` (e.g. `new PdfDestination(page2)`); an external link takes the URL in its constructor.

```csharp
// create a new pdf document
PdfDocument doc = new PdfDocument();
PdfPage page1 = doc.AddPage();
PdfPage page2 = doc.AddPage();
PdfRenderingResult result;

PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 14;
font.IsUnderline = true;

// external link in 2 steps: add the link text, then the link over its rectangle
PdfTextElement linkText1 = new PdfTextElement(0, 50,
    "External link (click to go to selectpdf.com)", font);
result = page1.Add(linkText1);
PdfExternalLinkElement extLink1 = new PdfExternalLinkElement(
    result.PdfPageLastRectangle, "http://selectpdf.com");
page1.Add(extLink1);

// internal link in 2 steps: add the link text, then the link over its rectangle
PdfTextElement linkText2 = new PdfTextElement(0, 100,
    "Internal link (click to go to the second page)", font);
result = page1.Add(linkText2);
PdfInternalLinkElement intLink1 = new PdfInternalLinkElement(
    result.PdfPageLastRectangle, new PdfDestination(page2));
page1.Add(intLink1);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
' create a new pdf document
Dim doc As New PdfDocument()
Dim page1 As PdfPage = doc.AddPage()
Dim page2 As PdfPage = doc.AddPage()
Dim result As PdfRenderingResult

Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 14
font.IsUnderline = True

' external link in 2 steps: add the link text, then the link over its rectangle
Dim linkText1 As New PdfTextElement(0, 50, _
    "External link (click to go to selectpdf.com)", font)
result = page1.Add(linkText1)
Dim extLink1 As New PdfExternalLinkElement(result.PdfPageLastRectangle, _
    "http://selectpdf.com")
page1.Add(extLink1)

' internal link in 2 steps: add the link text, then the link over its rectangle
Dim linkText2 As New PdfTextElement(0, 100, _
    "Internal link (click to go to the second page)", font)
result = page1.Add(linkText2)
Dim intLink1 As New PdfInternalLinkElement( _
    result.PdfPageLastRectangle, New PdfDestination(page2))
page1.Add(intLink1)

doc.Save("output.pdf")
doc.Close()
```

## Adding bookmarks manually

Bookmarks form a tree-structured document outline handled by the `PdfBookmark` class; `PdfDocument.Bookmarks` exposes the `PdfBookmarksCollection`. Add a top-level bookmark with `AddBookmark(String, PdfDestination)`, or a child bookmark with `AddBookmark(String, PdfDestination, PdfBookmark)` passing an existing bookmark as parent. The hierarchy can be rearranged through `PdfBookmark.Parent` and `PdfBookmark.ChildNodes`, and each bookmark has a `Color` and a `Style` (`PdfBookmarkStyle`). Set `doc.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines` to open the viewer with the bookmarks panel showing.

```csharp
PdfDocument doc = new PdfDocument();
doc.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines;

PdfPage page1 = doc.AddPage();
PdfPage page2 = doc.AddPage();
PdfRenderingResult result;

PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 14;

// first level bookmarks pointing to pages
PdfBookmark book2 = doc.AddBookmark("Second page",
    new PdfDestination(page2, new System.Drawing.PointF(0, 0)));

// other text on page 2
PdfTextElement text22 = new PdfTextElement(0, 400, "Text 2 on page 2", font);
result = page2.Add(text22);

// a level 2 bookmark (book2 as parent) with custom style and color
PdfBookmark book = doc.AddBookmark("Text 2",
    new PdfDestination(page2, result.PdfPageLastRectangle.Location), book2);
book.Color = System.Drawing.Color.Red;
book.Style = PdfBookmarkStyle.Italic;

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
doc.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines

Dim page1 As PdfPage = doc.AddPage()
Dim page2 As PdfPage = doc.AddPage()
Dim result As PdfRenderingResult

Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 14

' first level bookmark pointing to a page
Dim book2 As PdfBookmark = doc.AddBookmark("Second page", _
    New PdfDestination(page2, New System.Drawing.PointF(0, 0)))

' other text on page 2
Dim text22 As New PdfTextElement(0, 400, "Text 2 on page 2", font)
result = page2.Add(text22)

' a level 2 bookmark (book2 as parent) with custom style and color
Dim book As PdfBookmark = doc.AddBookmark("Text 2", _
    New PdfDestination(page2, result.PdfPageLastRectangle.Location), book2)
book.Color = System.Drawing.Color.Red
book.Style = PdfBookmarkStyle.Italic

doc.Save("output.pdf")
doc.Close()
```

## Automatic bookmarks from HTML headings

During conversion the converter can generate bookmarks automatically from selected HTML elements. The selection is set via `HtmlToPdfOptions.PdfBookmarkOptions`, a `PdfBookmarkOptions` instance whose single property `CssSelectors` takes an array of CSS selectors (e.g. `"H1"`, `"*.myclass"`, `"*#myid"`). Combine with `ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines` to show the bookmarks panel on open.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// generate bookmarks from H1 and H2 elements
converter.Options.PdfBookmarkOptions.CssSelectors = new string[] { "H1", "H2" };
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines;

PdfDocument doc = converter.ConvertUrl(url);
doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' generate bookmarks from H1 and H2 elements
converter.Options.PdfBookmarkOptions.CssSelectors = New String() {"H1", "H2"}
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines

Dim doc As PdfDocument = converter.ConvertUrl(url)
doc.Save("output.pdf")
doc.Close()
```

## Generating a table of contents with page numbers

A TOC must already exist at the top of the page (titles with IDs `TOC_Title{0}`, content targets with IDs `TOC_Target{0}`, all sharing a CSS class). Map those elements with `HtmlToPdfOptions.WebElementsMappingOptions.CssSelectors`, then after conversion read each element from `WebElementsMappingOptions.Result.GetElementByHtmlId(id)`. The returned `WebElement.PdfRectangles[0]` exposes `PageIndex` (target page number = `PageIndex + 1`) and `Rectangle`, which let you draw a dashed `PdfLineElement` and place a page-number `PdfTextElement` beside each TOC title.

```csharp
HtmlToPdf converter = new HtmlToPdf();
converter.Options.PdfBookmarkOptions.CssSelectors = new string[] { "*.bookmark" };
converter.Options.WebElementsMappingOptions.CssSelectors = new string[] { "*.toc" };
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines;

PdfDocument doc = converter.ConvertUrl(url);

// font for the page numbers
PdfFont pageNumberFont = doc.Fonts.Add(
    new System.Drawing.Font("Arial", 12, System.Drawing.FontStyle.Regular,
        System.Drawing.GraphicsUnit.Point), true);

float tocRight = doc.Pages[0].ClientRectangle.Width - 50;

for (int tocItem = 1; tocItem <= 13; tocItem++)
{
    WebElement tocTitle = converter.Options.WebElementsMappingOptions
        .Result.GetElementByHtmlId(String.Format("TOC_Title{0}", tocItem));
    WebElement tocTarget = converter.Options.WebElementsMappingOptions
        .Result.GetElementByHtmlId(String.Format("TOC_Target{0}", tocItem));
    if (tocTitle == null || tocTarget == null) continue;

    PdfPage tocPage = doc.Pages[tocTitle.PdfRectangles[0].PageIndex];
    System.Drawing.RectangleF tocTitleRectangle = tocTitle.PdfRectangles[0].Rectangle;
    int tocTargetPageNumber = tocTarget.PdfRectangles[0].PageIndex + 1;

    // dashed leader line from the title to the page number
    PdfLineElement dashedLine = new PdfLineElement(
        tocTitleRectangle.Right + 5, tocTitleRectangle.Y + tocTitleRectangle.Height - 4,
        tocRight, tocTitleRectangle.Y + tocTitleRectangle.Height - 4);
    dashedLine.LineStyle.LineWidth = 1;
    dashedLine.LineStyle.LineDashStyle = PdfLineDashStyle.Dash;
    dashedLine.ForeColor = System.Drawing.Color.Black;
    tocPage.Add(dashedLine);

    // the page number text element to the right of the title
    PdfTextElement pageNumberTextElement = new PdfTextElement(tocRight + 5,
        tocTitleRectangle.Y, -1, tocTitleRectangle.Height,
        tocTargetPageNumber.ToString(), pageNumberFont);
    pageNumberTextElement.HorizontalAlign = PdfTextHorizontalAlign.Left;
    pageNumberTextElement.VerticalAlign = PdfTextVerticalAlign.Middle;
    tocPage.Add(pageNumberTextElement);
}

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()
converter.Options.PdfBookmarkOptions.CssSelectors = New String() {"*.bookmark"}
converter.Options.WebElementsMappingOptions.CssSelectors = New String() {"*.toc"}
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseOutlines

Dim doc As PdfDocument = converter.ConvertUrl(url)

' font for the page numbers
Dim pageNumberFont As PdfFont = doc.Fonts.Add( _
    New System.Drawing.Font("Arial", 12, System.Drawing.FontStyle.Regular, _
        System.Drawing.GraphicsUnit.Point), True)

Dim tocRight As Single = doc.Pages(0).ClientRectangle.Width - 50

For tocItem As Integer = 1 To 13
    Dim tocTitle As WebElement = converter.Options.WebElementsMappingOptions _
        .Result.GetElementByHtmlId(String.Format("TOC_Title{0}", tocItem))
    Dim tocTarget As WebElement = converter.Options.WebElementsMappingOptions _
        .Result.GetElementByHtmlId(String.Format("TOC_Target{0}", tocItem))
    If tocTitle Is Nothing OrElse tocTarget Is Nothing Then Continue For

    Dim tocPage As PdfPage = doc.Pages(tocTitle.PdfRectangles(0).PageIndex)
    Dim tocTitleRectangle As System.Drawing.RectangleF = tocTitle.PdfRectangles(0).Rectangle
    Dim tocTargetPageNumber As Integer = tocTarget.PdfRectangles(0).PageIndex + 1

    ' dashed leader line from the title to the page number
    Dim dashedLine As New PdfLineElement( _
        tocTitleRectangle.Right + 5, tocTitleRectangle.Y + tocTitleRectangle.Height - 4, _
        tocRight, tocTitleRectangle.Y + tocTitleRectangle.Height - 4)
    dashedLine.LineStyle.LineWidth = 1
    dashedLine.LineStyle.LineDashStyle = PdfLineDashStyle.Dash
    dashedLine.ForeColor = System.Drawing.Color.Black
    tocPage.Add(dashedLine)

    ' the page number text element to the right of the title
    Dim pageNumberTextElement As New PdfTextElement(tocRight + 5, _
        tocTitleRectangle.Y, -1, tocTitleRectangle.Height, _
        tocTargetPageNumber.ToString(), pageNumberFont)
    pageNumberTextElement.HorizontalAlign = PdfTextHorizontalAlign.Left
    pageNumberTextElement.VerticalAlign = PdfTextVerticalAlign.Middle
    tocPage.Add(pageNumberTextElement)
Next

doc.Save("output.pdf")
doc.Close()
```

## PDF open actions

`PdfDocument.OpenAction` is a `PdfDocumentOpenAction` whose `Action` property defines what the viewer does on open. Set it to `PdfActionGoTo(PdfDestination)` to open at a specific page/location, or to `PdfActionJavaScript(string)` to run JavaScript (e.g. `"print()"`, `"app.alert(...)"`, `"zoom = 150"`, or `"print({bUI: false, bSilent: true, bShrinkToFit: true})"` for silent print).

```csharp
HtmlToPdf converter = new HtmlToPdf();
PdfDocument doc = converter.ConvertUrl(url);

// open at the second page
if (doc.Pages.Count > 1)
{
    PdfPage secondPage = doc.Pages[1];
    PdfDestination secondPageDestination = new PdfDestination(secondPage);
    doc.OpenAction.Action = new PdfActionGoTo(secondPageDestination);
}

// or: run JavaScript on open (show the print dialog)
doc.OpenAction.Action = new PdfActionJavaScript("print()");

doc.Save("output.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()
Dim doc As PdfDocument = converter.ConvertUrl(url)

' open at the second page
If doc.Pages.Count > 1 Then
    Dim secondPage As PdfPage = doc.Pages(1)
    Dim secondPageDestination As New PdfDestination(secondPage)
    doc.OpenAction.Action = New PdfActionGoTo(secondPageDestination)
End If

' or: run JavaScript on open (show the print dialog)
doc.OpenAction.Action = New PdfActionJavaScript("print()")

doc.Save("output.pdf")
doc.Close()
```

See also: html-to-pdf.md, page-setup-headers-footers.md, api-cheatsheet.md
