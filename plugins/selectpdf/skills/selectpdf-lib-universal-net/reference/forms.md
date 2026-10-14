# PDF forms

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## Form filling

SelectPdf can be used to open existing PDF documents that contain AcroForms form fields, get information about the form fields (name, type, value, status) fill the form fields and change some of their properties (like read-only or flatten).

This can be done using `PdfFormManager` object. Using it, the list of fields can be accessed through the `PdfFormManager.Fields` property. The following types of fields can be handled by SelectPdf:

- Textbox using `PdfFormFieldTextBox`. For a textbox field, the field value can be set using the property `PdfFormFieldTextBox.Text`.
- Checkbox using `PdfFormFieldCheckBox`. For a checkbox field, the checked status can be set or unset using the property `PdfFormFieldCheckBox.Checked`.
- Listbox using `PdfFormFieldListBox`. For a list box field, the selected items can be set using either property `PdfFormFieldListBox.SelectedIndexes` or `PdfFormFieldListBox.SelectedValues`. The number of items in the list can be obtained using `PdfFormFieldListBox.ItemsCount` property. The list of items from the listbox can be accessed using the `PdfFormFieldListBox.Items` property. `PdfFormFieldListBox.MultiSelect` can be used to determine if more than 1 item can be selected from the list box.
- Combo box using `PdfFormFieldComboBox`. For a combo box field, the selected item can be set using either property `PdfFormFieldComboBox.SelectedIndex` or `PdfFormFieldComboBox.SelectedValue`. The number of items in the list can be obtained using `PdfFormFieldComboBox.ItemsCount` property. The list of items from the combo box can be accessed using the `PdfFormFieldComboBox.Items` property.
- Radio buttons list using `PdfFormFieldRadioButtonList`. For a radio buttons list field, the selected item can be set using either property `PdfFormFieldRadioButtonList.SelectedIndex` or `PdfFormFieldRadioButtonList.SelectedValue`. The number of items in the list can be obtained using `PdfFormFieldRadioButtonList.ItemsCount` property. The list of items can be accessed using the `PdfFormFieldRadioButtonList.Items` property.

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
