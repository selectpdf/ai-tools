# AcroForms and PDF compression (Select.Pdf.Extras)

Filling existing AcroForm fields and compressing existing PDF documents are provided by the optional `SelectPdf.Extras` package (the forms engine on `SelectPdf.Forms.PdfFormManager` and the `SelectPdf.Compressor.PdfCompressor` both require it); compressing newly generated PDFs via `PdfDocument.CompressionLevel` is part of the core library.

## Filling existing AcroForm fields

Open a PDF that contains AcroForm fields with `PdfFormManager` and call `Load(file)`. Access fields through the `Fields` collection by name, cast each to its concrete field type, set the value, then `Save()` (returns `byte[]`) or `Save("output.pdf")`, and `Close()`. The field types and their value members are: `PdfFormFieldTextBox` (`Text`), `PdfFormFieldCheckBox` (`Checked`), `PdfFormFieldListBox` (`SelectedIndexes` / `SelectedValues`, `MultiSelect`, `Items`, `ItemsCount`), `PdfFormFieldComboBox` (`SelectedIndex` / `SelectedValue`, `Items`, `ItemsCount`), and `PdfFormFieldRadioButtonList` (`SelectedIndex` / `SelectedValue`, `Items`, `ItemsCount`). Every field also exposes `Name`, `FullName`, `DisplayPage`, `DisplayRectangle`, `Widgets`, `ReadOnly`, and `Flatten`; the whole form can be marked read-only or flattened via `PdfFormManager.ReadOnly` and `PdfFormManager.Flatten`. Starting with version 21.1 the new forms engine is used by replacing `SelectPdf.PdfFormManager` with `SelectPdf.Forms.PdfFormManager` from the `SelectPdf.Extras` package.

```csharp
// the initial file
string file = @"C:\Path\to\w-9.pdf";

// load the pdf form manager
PdfFormManager form = new PdfFormManager();
form.Load(file);

// fill some text fields
PdfFormFieldTextBox name = form.Fields["f1_1"] as PdfFormFieldTextBox;
name.Text = "This is my name";

PdfFormFieldTextBox address = form.Fields["f1_7"] as PdfFormFieldTextBox;
address.Text = "This is my address";

// set a check box
PdfFormFieldCheckBox clasifIndividual = form.Fields["c1_1"] as PdfFormFieldCheckBox;
clasifIndividual.Checked = true;

// optionally flatten the whole form
form.Flatten = true;

// save pdf document
form.Save("output.pdf");

// close pdf document
form.Close();
```

```vbnet
' the initial file
Dim file As String = "C:\Path\to\w-9.pdf"

' load the pdf form manager
Dim form As New PdfFormManager()
form.Load(file)

' fill some text fields
Dim name As PdfFormFieldTextBox = TryCast(form.Fields("f1_1"), PdfFormFieldTextBox)
name.Text = "This is my name"

Dim address As PdfFormFieldTextBox = TryCast(form.Fields("f1_7"), PdfFormFieldTextBox)
address.Text = "This is my address"

' set a check box
Dim clasifIndividual As PdfFormFieldCheckBox = TryCast(form.Fields("c1_1"), PdfFormFieldCheckBox)
clasifIndividual.Checked = True

' optionally flatten the whole form
form.Flatten = True

' save pdf document
form.Save("output.pdf")

' close pdf document
form.Close()
```

## Compressing a new PDF document

For newly created documents, set `PdfDocument.CompressionLevel` to a `PdfCompressionLevel` value. The three documented levels are `NoCompression`, `Normal`, and `Best`.

```csharp
// create a new pdf document
PdfDocument doc = new PdfDocument();
doc.Margins = new PdfMargins(20, 20, 20, 20);

// set compression level
doc.CompressionLevel = PdfCompressionLevel.Best;

// add content
PdfFont font = doc.AddFont(PdfStandardFont.Helvetica);
font.Size = 16;
PdfPage page = doc.AddPage();
PdfTextElement text = new PdfTextElement(0, 0, "Some long text...", font);
page.Add(text);

// save and close pdf document
doc.Save("output.pdf");
doc.Close();
```

```vbnet
' create a new pdf document
Dim doc As New PdfDocument()
doc.Margins = New PdfMargins(20, 20, 20, 20)

' set compression level
doc.CompressionLevel = PdfCompressionLevel.Best

' add content
Dim font As PdfFont = doc.AddFont(PdfStandardFont.Helvetica)
font.Size = 16
Dim page As PdfPage = doc.AddPage()
Dim text As New PdfTextElement(0, 0, "Some long text...", font)
page.Add(text)

' save and close pdf document
doc.Save("output.pdf")
doc.Close()
```

## Compressing an existing PDF document

To shrink an existing file, use `SelectPdf.Compressor.PdfCompressor` (requires the `SelectPdf.Extras` package). Call `Load(file)`, set the compression options, then `Save(outputFile)`. The options are: `StreamsCompressionLevel` (a `SelectPdf.Compressor.PdfCompressionLevel` such as `BestCompression`), `StructureCompressionEnabled`, `RemoveDuplicateFonts`, `RemoveUnusedFontGlyphs`, `ImagesCompressionEnabled`, and `ImagesCompressionLevel` (0 = no compression, 100 = best compression). After saving, `DocumentInformation` exposes details and results including `PageCount`, `Title`, `Subject`, `Author`, `Producer`, `CreationDate`, `LastModifiedDate`, `InitialFileSize`, and `FinalFileSize`.

```csharp
// load existing file
string file = @"C:\Path\to\document.pdf";
SelectPdf.Compressor.PdfCompressor compressor = new SelectPdf.Compressor.PdfCompressor();
compressor.Load(file);

// set compression options
compressor.StreamsCompressionLevel = SelectPdf.Compressor.PdfCompressionLevel.BestCompression;
compressor.StructureCompressionEnabled = true;
compressor.RemoveDuplicateFonts = true;
compressor.RemoveUnusedFontGlyphs = true;
compressor.ImagesCompressionEnabled = true;
compressor.ImagesCompressionLevel = 20;

// save compressed document
compressor.Save(file + "-compressed.pdf");

// inspect the result of the compression
Console.WriteLine("Pages: " + compressor.DocumentInformation.PageCount);
Console.WriteLine("Initial file size: " + compressor.DocumentInformation.InitialFileSize);
Console.WriteLine("Final file size: " + compressor.DocumentInformation.FinalFileSize);
```

```vbnet
' load existing file
Dim file As String = "C:\Path\to\document.pdf"
Dim compressor As SelectPdf.Compressor.PdfCompressor = New SelectPdf.Compressor.PdfCompressor()
compressor.Load(file)

' set compression options
compressor.StreamsCompressionLevel = SelectPdf.Compressor.PdfCompressionLevel.BestCompression
compressor.StructureCompressionEnabled = True
compressor.RemoveDuplicateFonts = True
compressor.RemoveUnusedFontGlyphs = True
compressor.ImagesCompressionEnabled = True
compressor.ImagesCompressionLevel = 20

' save compressed document
compressor.Save(file & "-compressed.pdf")

' inspect the result of the compression
Console.WriteLine("Pages: " & compressor.DocumentInformation.PageCount)
Console.WriteLine("Initial file size: " & compressor.DocumentInformation.InitialFileSize)
Console.WriteLine("Final file size: " & compressor.DocumentInformation.FinalFileSize)
```

See also: pdf-creation-elements.md, edit-merge-split-stamp.md, api-cheatsheet.md
