# Security, permissions, viewer preferences and digital signatures

How to encrypt, password-protect, set permissions, control viewer behavior, and digitally sign PDFs with the commercial Select.Pdf .NET library.

## Encrypting and password-protecting during HTML→PDF conversion

Set passwords and permissions for the converted document through `HtmlToPdfOptions.SecurityOptions` (a `PdfSecurityOptions`), reached via `converter.Options.SecurityOptions`. `OwnerPassword` controls full access; `UserPassword` is required to open the document. The permission flags that appear are `CanAssembleDocument`, `CanCopyContent`, `CanEditAnnotations`, `CanEditContent`, `CanFillFormFields`, and `CanPrint`.

```csharp
// instantiate a html to pdf converter object
HtmlToPdf converter = new HtmlToPdf();

// set document passwords
converter.Options.SecurityOptions.OwnerPassword = "test1";
converter.Options.SecurityOptions.UserPassword = "test2";

// set document permissions
converter.Options.SecurityOptions.CanAssembleDocument = false;
converter.Options.SecurityOptions.CanCopyContent = true;
converter.Options.SecurityOptions.CanEditAnnotations = true;
converter.Options.SecurityOptions.CanEditContent = true;
converter.Options.SecurityOptions.CanFillFormFields = true;
converter.Options.SecurityOptions.CanPrint = true;

// create a new pdf document converting an url
PdfDocument doc = converter.ConvertUrl(url);
doc.Save("output.pdf");
doc.Close();
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New HtmlToPdf()

' set document passwords
converter.Options.SecurityOptions.OwnerPassword = "test1"
converter.Options.SecurityOptions.UserPassword = "test2"

' set document permissions
converter.Options.SecurityOptions.CanAssembleDocument = False
converter.Options.SecurityOptions.CanCopyContent = True
converter.Options.SecurityOptions.CanEditAnnotations = True
converter.Options.SecurityOptions.CanEditContent = True
converter.Options.SecurityOptions.CanFillFormFields = True
converter.Options.SecurityOptions.CanPrint = True

' create a new pdf document converting an url
Dim doc As PdfDocument = converter.ConvertUrl(url)
doc.Save("output.pdf")
doc.Close()
```

## Securing a PDF document (PdfDocument.Security)

For an in-memory `PdfDocument`, the same settings are applied through its `Security` property (a `PdfSecurityOptions`). It exposes `OwnerPassword`, `UserPassword`, and the permission flags `CanAssembleDocument`, `CanCopyContent`, `CanEditAnnotations`, `CanEditContent`, `CanFillFormFields`, and `CanPrint`.

```csharp
// set document passwords
doc.Security.OwnerPassword = "test1";
doc.Security.UserPassword = "test2";

// set document permissions
doc.Security.CanAssembleDocument = false;
doc.Security.CanCopyContent = true;
doc.Security.CanEditAnnotations = true;
doc.Security.CanEditContent = true;
doc.Security.CanFillFormFields = true;
doc.Security.CanPrint = true;
```

```vbnet
' set document passwords
doc.Security.OwnerPassword = "test1"
doc.Security.UserPassword = "test2"

' set document permissions
doc.Security.CanAssembleDocument = False
doc.Security.CanCopyContent = True
doc.Security.CanEditAnnotations = True
doc.Security.CanEditContent = True
doc.Security.CanFillFormFields = True
doc.Security.CanPrint = True
```

## Advanced security on an existing PDF (PdfSecurityManager)

`PdfSecurityManager` applies advanced security to an existing PDF file: `Load(file)`, configure, then `Save(...)` and `Close()`. It lets you choose the encryption algorithm via `EncryptionAlgorithm` (`PdfEncryptionAlgorithm.RC4` or `PdfEncryptionAlgorithm.AES`) and the key length via `EncryptionKeySize` (`PdfEncryptionKeySize.EncryptKey256Bit`; the documented sizes are 40, 128 or 256 bits). Beyond the standard permission flags (note the manager uses `CanFillFields`, not `CanFillFormFields`), it adds `CanAccessibilityCopyContent` and `CanFullQualityPrint`, and can also digitally sign the document with `Sign(...)`.

```csharp
// load the pdf document using the advanced security manager
PdfSecurityManager security = new PdfSecurityManager();
security.Load(file);

// encryption algorithm and key length
security.EncryptionAlgorithm = PdfEncryptionAlgorithm.AES;
security.EncryptionKeySize = PdfEncryptionKeySize.EncryptKey256Bit;

// set document passwords
security.OwnerPassword = "test1";
security.UserPassword = "test2";

// set document permissions
security.CanAssembleDocument = true;
security.CanCopyContent = true;
security.CanAccessibilityCopyContent = true;
security.CanEditAnnotations = true;
security.CanEditContent = true;
security.CanFillFields = true;
security.CanPrint = true;
security.CanFullQualityPrint = true;

// add the digital signature
security.Sign(certFile, "selectpdf");

// save and close
security.Save("output.pdf");
security.Close();
```

```vbnet
' load the pdf document using the advanced security manager
Dim security As New PdfSecurityManager()
security.Load(file)

' encryption algorithm and key length
security.EncryptionAlgorithm = PdfEncryptionAlgorithm.AES
security.EncryptionKeySize = PdfEncryptionKeySize.EncryptKey256Bit

' set document passwords
security.OwnerPassword = "test1"
security.UserPassword = "test2"

' set document permissions
security.CanAssembleDocument = True
security.CanCopyContent = True
security.CanAccessibilityCopyContent = True
security.CanEditAnnotations = True
security.CanEditContent = True
security.CanFillFields = True
security.CanPrint = True
security.CanFullQualityPrint = True

' add the digital signature
security.Sign(certFile, "selectpdf")

' save and close
security.Save("output.pdf")
security.Close()
```

`PdfSecurityManager` exposes five `Sign(...)` overloads: minimal `Sign(certificateFileName, certificatePassword)`; `Sign(..., signatureName)`; `Sign(..., signatureName, reason, location, contactInfo)`; `Sign(..., signatureName, reason, location, contactInfo, pageIndex, bounds)` which renders a visible signature box on the zero-based `pageIndex` inside a `System.Drawing.RectangleF`; and `Sign(..., signatureName, reason, location, contactInfo, pageIndex, bounds, timeStampAuthority)` which adds a trusted RFC 3161 timestamp from a `TimeStampAuthority`. A `TimeStampAuthority(Uri tsaServer)` exposes `Server`, `UserName`, `Password`, and `TimeOut` (seconds).

```csharp
PdfSecurityManager security = new PdfSecurityManager();
security.Load("contract.pdf");

TimeStampAuthority tsa = new TimeStampAuthority(new Uri("https://tsa.example.com/rfc3161"));
tsa.UserName = "tsa-account";
tsa.Password = "tsa-password";
tsa.TimeOut  = 30;

security.Sign(
    "cert.pfx", "pfxpwd",
    "Signed and time-stamped",
    "Contract approval",
    "Amsterdam, NL",
    "legal@example.com",
    0,
    new System.Drawing.RectangleF(400, 40, 150, 50),
    tsa);

security.Save("contract-signed.pdf");
security.Close();
```

```vbnet
Dim security As New PdfSecurityManager()
security.Load("contract.pdf")

Dim tsa As New TimeStampAuthority(New Uri("https://tsa.example.com/rfc3161"))
tsa.UserName = "tsa-account"
tsa.Password = "tsa-password"
tsa.TimeOut  = 30

security.Sign(
    "cert.pfx", "pfxpwd",
    "Signed and time-stamped",
    "Contract approval",
    "Amsterdam, NL",
    "legal@example.com",
    0,
    New System.Drawing.RectangleF(400, 40, 150, 50),
    tsa)

security.Save("contract-signed.pdf")
security.Close()
```

## Viewer preferences

Viewer preferences control how a PDF is displayed when opened. On a `PdfDocument`, use the `ViewerPreferences` property (a `PdfViewerPreferences`); for an HTML→PDF conversion, use `converter.Options.ViewerPreferences` (an `HtmlToPdfViewerPreferences`) — both expose the same members. The boolean flags are `HideToolbar`, `HideMenuBar`, `HideWindowUI`, `FitWindow`, `CenterWindow`, and `DisplayDocTitle`. The enum members are `PageMode` (`PdfViewerPageMode`: `UseNone`, `UseOutlines`, `UseThumbs`, `FullScreen`, `UseOC`, `UseAttachments`), `PageLayout` (`PdfViewerPageLayout`: `SinglePage`, `OneColumn`, `TwoColumnLeft`, `TwoColumnRight`), and `NonFullScreenPageMode` (`PdfViewerFullScreenExitMode`: `UseNone`, `UseOutlines`, `UseThumbs`, `UseOC`).

```csharp
// on a converter (HtmlToPdfViewerPreferences)
converter.Options.ViewerPreferences.CenterWindow = true;
converter.Options.ViewerPreferences.DisplayDocTitle = true;
converter.Options.ViewerPreferences.FitWindow = true;
converter.Options.ViewerPreferences.HideMenuBar = true;
converter.Options.ViewerPreferences.HideToolbar = true;
converter.Options.ViewerPreferences.HideWindowUI = true;

converter.Options.ViewerPreferences.PageLayout = PdfViewerPageLayout.SinglePage;
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseNone;
converter.Options.ViewerPreferences.NonFullScreenPageMode = PdfViewerFullScreenExitMode.UseNone;

// on an existing document (PdfViewerPreferences)
doc.ViewerPreferences.CenterWindow = true;
doc.ViewerPreferences.PageLayout = PdfViewerPageLayout.SinglePage;
```

```vbnet
' on a converter (HtmlToPdfViewerPreferences)
converter.Options.ViewerPreferences.CenterWindow = True
converter.Options.ViewerPreferences.DisplayDocTitle = True
converter.Options.ViewerPreferences.FitWindow = True
converter.Options.ViewerPreferences.HideMenuBar = True
converter.Options.ViewerPreferences.HideToolbar = True
converter.Options.ViewerPreferences.HideWindowUI = True

converter.Options.ViewerPreferences.PageLayout = PdfViewerPageLayout.SinglePage
converter.Options.ViewerPreferences.PageMode = PdfViewerPageMode.UseNone
converter.Options.ViewerPreferences.NonFullScreenPageMode = PdfViewerFullScreenExitMode.UseNone

' on an existing document (PdfViewerPreferences)
doc.ViewerPreferences.CenterWindow = True
doc.ViewerPreferences.PageLayout = PdfViewerPageLayout.SinglePage
```

## Digital signatures (PdfDigitalSignatureElement)

To place a visible signature inside a `PdfDocument`, add a `PdfDigitalSignatureElement`. Load the PKCS#12 certificate (a `.pfx` or `.p12` file) with `PdfDigitalCertificatesStore.GetCertificates(certFile, password)`, which returns a `PdfDigitalCertificatesCollection`; index it to get a `PdfDigitalCertificate`. Construct the signature from a `RectangleF` (here `result.PdfPageLastRectangle` from rendering an image to sign over) and the certificate, set the appearance/metadata properties `Reason`, `ContactInfo`, and `Location`, then add it to the page.

```csharp
// create a new pdf document and page
PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

// image displayed under the digital signature
string imgFile = "logo.png";
string certFile = "selectpdf.pfx";
PdfRenderingResult result;

PdfImageElement img = new PdfImageElement(0, 0, imgFile);
result = page.Add(img);

// get the PKCS#12 certificate from file
PdfDigitalCertificatesCollection certificates =
    PdfDigitalCertificatesStore.GetCertificates(certFile, "selectpdf");
PdfDigitalCertificate certificate = certificates[0];

// create the digital signature object
PdfDigitalSignatureElement signature =
    new PdfDigitalSignatureElement(result.PdfPageLastRectangle, certificate);
signature.Reason = "SelectPdf";
signature.ContactInfo = "SelectPdf";
signature.Location = "SelectPdf";
page.Add(signature);

doc.Save("output.pdf");
doc.Close();
```

```vbnet
' create a new pdf document and page
Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

' image displayed under the digital signature
Dim imgFile As String = "logo.png"
Dim certFile As String = "selectpdf.pfx"
Dim result As PdfRenderingResult

Dim img As New PdfImageElement(0, 0, imgFile)
result = page.Add(img)

' get the PKCS#12 certificate from file
Dim certificates As PdfDigitalCertificatesCollection = _
    PdfDigitalCertificatesStore.GetCertificates(certFile, "selectpdf")
Dim certificate As PdfDigitalCertificate = certificates(0)

' create the digital signature object
Dim signature As New PdfDigitalSignatureElement( _
    result.PdfPageLastRectangle, certificate)
signature.Reason = "SelectPdf"
signature.ContactInfo = "SelectPdf"
signature.Location = "SelectPdf"
page.Add(signature)

doc.Save("output.pdf")
doc.Close()
```

See also: edit-merge-split-stamp.md, forms-and-extras.md, deployment-azure-troubleshooting.md, api-cheatsheet.md
