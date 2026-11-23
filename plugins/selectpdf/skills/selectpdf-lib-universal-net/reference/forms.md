# PDF forms

Converted from the shipped SelectPdf.Universal v26.6 help topics. C# and VB.NET samples are the official ones.

## Form filling

SelectPdf can be used to open existing PDF documents that contain AcroForms form fields (or an XFA form, see XFA Forms), get information about the form fields (name, type, value, status) fill the form fields and change some of their properties (like read-only or flatten).

This can be done using `PdfFormManager` object. Using it, the list of fields can be accessed through the `PdfFormManager.Fields` property. The following types of fields can be handled by SelectPdf:

- Textbox using `PdfFormFieldTextBox`. For a textbox field, the field value can be set using the property `PdfFormFieldTextBox.Text`.
- Checkbox using `PdfFormFieldCheckBox`. For a checkbox field, the checked status can be set or unset using the property `PdfFormFieldCheckBox.Checked`.
- Listbox using `PdfFormFieldListBox`. For a list box field, the selected items can be set using either property `PdfFormFieldListBox.SelectedIndexes` or `PdfFormFieldListBox.SelectedValues`. The number of items in the list can be obtained using `PdfFormFieldListBox.ItemsCount` property. The list of items from the listbox can be accessed using the `PdfFormFieldListBox.Items` property. `PdfFormFieldListBox.MultiSelect` can be used to determine if more than 1 item can be selected from the list box. `PdfFormFieldListBox.ItemTexts` gives the text each item shows, which can differ from its export value.
- Combo box using `PdfFormFieldComboBox`. For a combo box field, the selected item can be set using either property `PdfFormFieldComboBox.SelectedIndex` or `PdfFormFieldComboBox.SelectedValue`. The number of items in the list can be obtained using `PdfFormFieldComboBox.ItemsCount` property. The list of items from the combo box can be accessed using the `PdfFormFieldComboBox.Items` property. `PdfFormFieldComboBox.ItemTexts` gives the text each item shows, which can differ from its export value.
- Radio buttons list using `PdfFormFieldRadioButtonList`. For a radio buttons list field, the selected item can be set using either property `PdfFormFieldRadioButtonList.SelectedIndex` or `PdfFormFieldRadioButtonList.SelectedValue`. The number of items in the list can be obtained using `PdfFormFieldRadioButtonList.ItemsCount` property. The list of items can be accessed using the `PdfFormFieldRadioButtonList.Items` property. `PdfFormFieldRadioButtonList.ItemTexts` gives the caption each button shows on an XFA form; an AcroForm radio button shows no text of its own, so its export value is given.

For all form fields, the following properties can be accessed:

- `PdfFormField.Name` - Gets or sets the name of the field.
- `PdfFormField.FullName` - Gets the fully-qualified field name (parent-qualified for nested fields). Use this when the field name in the PDF is qualified (for example `address.city`).
- `PdfFormField.DisplayPage` - Gets the zero-based index of the page on which the field is displayed.
- `PdfFormField.DisplayRectangle` - Gets the bounds of the field on its page as a `RectangleF`.
- `PdfFormField.Widgets` - Gets the collection of widget annotations that represent the field on the page. A single form field can have more than one widget (for example a check box repeated in the header and footer).
- `PdfFormField.ReadOnly` - Gets or sets a value indicating whether the field is read-only or not.
- `PdfFormField.Flatten` - Gets or sets a value indicating whether to flatten this form field or not.

When a check box field has multiple widgets that represent the same logical value on different pages, each widget can be read or written individually through `PdfFormFieldCheckBox.GetWidgetCheckedValue(int)` and `PdfFormFieldCheckBox.SetWidgetCheckedValue(bool, int)`, where the integer is the widget index in the `PdfFormField.Widgets` collection. Setting `PdfFormFieldCheckBox.Checked` applies to every widget at once.

Individual fields can be marked read-only using the `PdfFormField.ReadOnly` property, and the whole PDF form can be flattened using `PdfFormManager.Flatten`.

### Code Sample

```csharp
// load the pdf form manager
PdfFormManager form = new PdfFormManager();
form.Load(file);

// fill some fields
PdfFormFieldTextBox txtField =
    form.Fields["textfieldname"] as PdfFormFieldTextBox;
txtField.Text = "Some text";

PdfFormFieldCheckBox chkField =
    form.Fields["checkboxfieldname"] as PdfFormFieldCheckBox;
chkField.Checked = true;

// save pdf document
form.Save("Sample.pdf");

// close pdf document
form.Close();
```

```vbnet
' load the pdf form manager
Dim form As New PdfFormManager()
form.Load(file)

' fill some fields
Dim txtField As PdfFormFieldTextBox = _
    TryCast(form.Fields("textfieldname"), PdfFormFieldTextBox)
txtField.Text = "Some text"

Dim chkField As PdfFormFieldCheckBox = _
    TryCast(form.Fields("checkboxfieldname"), PdfFormFieldCheckBox)
chkField.Checked = True

' save pdf document
form.Save("Sample.pdf")

' close pdf document
form.Close()
```

### Saving a Filled Form

By default `PdfFormManager.Save(string)` performs an incremental update: the loaded document is written unchanged and the changes are appended after it. That keeps a signature made before the form was filled verifiable. Set `PdfFormManager.CompactSave` to True to write a new, compact document instead - smaller, especially for forms that also carry an XFA form, but any signature the document carried no longer verifies.

```csharp
PdfFormManager manager = new PdfFormManager();
manager.Load("form.pdf");
((PdfFormFieldTextBox)manager.Fields["name"]).Text = "John Smith";

// write a compact document instead of appending the changes
manager.CompactSave = true;
manager.Save("form-filled.pdf");
manager.Close();
```

```vbnet
Dim manager As New PdfFormManager()
manager.Load("form.pdf")
DirectCast(manager.Fields("name"), PdfFormFieldTextBox).Text = "John Smith"

' write a compact document instead of appending the changes
manager.CompactSave = True
manager.Save("form-filled.pdf")
manager.Close()
```

### XFA Forms

Some forms are defined in XFA (XML Forms Architecture), as Adobe LiveCycle Designer makes them. A form that lays itself out (a dynamic XFA form) has its fields only in its XFA template: the document carries one placeholder page and no AcroForm fields, and a viewer builds the pages from the template. A hybrid form carries AcroForm fields on its own pages beside its XFA.

`PdfFormManager` fills a form that lays itself out through the same field types: a text, numeric, date or password field is a `PdfFormFieldTextBox`, a check box a `PdfFormFieldCheckBox`, a drop-down list a `PdfFormFieldComboBox`, a list a `PdfFormFieldListBox`, a group of radio buttons a `PdfFormFieldRadioButtonList`, and any other field (a button, a signature, an image) a `PdfFormField`. A field is named by its path in the form (`PdfFormField.FullName`, such as `form1[0].Page1[0].Name[0]`; the `[0]` indexes can be left out), or by its own name for the first field of that name. Its page and rectangle are those of the form as laid out, the pages the form has once flattened.

Setting a value is typing it, or choosing it, and leaving the field, as in Acrobat: the form's scripts for the change run, then its calculations and its validation. A number or a date is set and read as the form keeps it (a date as yyyy-MM-dd, a number with a period); `PdfFormField.FormattedValue` is what the form shows. What the form's scripts report - a value its validation refuses, the result of its own check button - is in `PdfFormManager.ScriptMessages`, and `PdfFormManager.Click(PdfFormField)` presses one of the form's buttons, and `PdfFormManager.ScriptErrors` holds the errors the form's scripts failed with (what Acrobat's JavaScript console shows). To build a page for a form, read what each field shows the user: `PdfFormField.Caption` (the label beside it), `PdfFormField.ToolTip`, `PdfFormField.Required`, `PdfFormField.Visible` and, for a text field, `PdfFormFieldTextBox.Multiline`; ToolTip, Required, Visible and Multiline are read from an AcroForm field too. The form is saved as the same dynamic form, or, with `PdfFormManager.Flatten`, as the pages it lays out with the values it holds.

`PdfFormManager.XfaMode` chooses how a form defined in XFA is filled, for the documents loaded after it is set:

- `Auto` (the default): a form that lays itself out is filled as XFA; a hybrid form is filled through its AcroForm fields and saved without its XFA.
- `Xfa`: as Auto, but a hybrid form keeps its XFA, given the same values as its fields, so a viewer that shows the XFA shows them too.
- `AcroForm`: the XFA is ignored. A form that lays itself out then has no fields.

`PdfFormManager.IsXfaForm` says whether the loaded form is defined in XFA.

A signature field of a form that lays itself out is signed by naming it in a `Sign` call of `PdfSecurityManager` (with a certificate file or an external signer), by its path or its own name, as Acrobat signs it: the form as laid out is drawn into the pages and saved as an update, a signature field is made where the form's is, and the signature fills it and covers the whole file. The signed document still opens as an XFA form. The same name works for a signature in two steps: `PrepareExternalSignature`, then `CompleteExternalSignature` with the name the field was prepared with.

```csharp
PdfSecurityManager security = new PdfSecurityManager();
security.Load("xfa-form-filled.pdf");
security.Sign("certificate.pfx", "pfxPassword", "Signature",
    "Approved", "Bucharest", "");
security.Save("xfa-form-signed.pdf");
security.Close();
```

```vbnet
Dim security As New PdfSecurityManager()
security.Load("xfa-form-filled.pdf")
security.Sign("certificate.pfx", "pfxPassword", "Signature",
    "Approved", "Bucharest", "")
security.Save("xfa-form-signed.pdf")
security.Close()
```

```csharp
PdfFormManager manager = new PdfFormManager();
manager.XfaMode = PdfFormXfaMode.Xfa;
manager.Load("xfa-form.pdf");

PdfFormFieldTextBox name =
    manager.Fields["form1.Page1.Name"] as PdfFormFieldTextBox;
name.Text = "John Smith";
PdfFormFieldTextBox birth =
    manager.Fields["form1.Page1.BirthDate"] as PdfFormFieldTextBox;
birth.Text = "1980-05-17";
System.Console.WriteLine(birth.FormattedValue);

// the form's own check button, and what it reported
manager.Click(manager.Fields["Check"]);
foreach (string message in manager.ScriptMessages)
    System.Console.WriteLine(message);

manager.Save("xfa-form-filled.pdf");
manager.Close();
```

```vbnet
Dim manager As New PdfFormManager()
manager.XfaMode = PdfFormXfaMode.Xfa
manager.Load("xfa-form.pdf")

Dim name As PdfFormFieldTextBox =
    TryCast(manager.Fields("form1.Page1.Name"), PdfFormFieldTextBox)
name.Text = "John Smith"
Dim birth As PdfFormFieldTextBox =
    TryCast(manager.Fields("form1.Page1.BirthDate"), PdfFormFieldTextBox)
birth.Text = "1980-05-17"
System.Console.WriteLine(birth.FormattedValue)

' the form's own check button, and what it reported
manager.Click(manager.Fields("Check"))
For Each message As String In manager.ScriptMessages
    System.Console.WriteLine(message)
Next

manager.Save("xfa-form-filled.pdf")
manager.Close()
```
