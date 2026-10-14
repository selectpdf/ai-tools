# Security, encryption and digital signatures

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## Security settings

Select.Pdf can be used to set the basic security settings for the generated pdf files and also manage different permissions:

- Owner Password
- User Password
- Assemble Document permission
- Copy Content permission
- Edit Annotations permission
- Edit Content permission
- Fill Form Fields permission
- Print permission

This can be done using `PdfDocument.Security` property of the `PdfDocument` object.

The strength of the encryption is set with `PdfDocumentSecurity.KeySize` and `PdfDocumentSecurity.Algorithm`. The supported combinations are:

- RC4 with a 40-bit or 128-bit key (`PdfEncryptionKeySize.EncryptKey40Bit`, `PdfEncryptionKeySize.EncryptKey128Bit`). Broken; for old viewers only.
- AES with a 128-bit or 256-bit key (`PdfEncryptionAlgorithm.AES` with `PdfEncryptionKeySize.EncryptKey128Bit` or `PdfEncryptionKeySize.EncryptKey256Bit`).
- AES-256 revision 6 (`PdfEncryptionKeySize.EncryptKey256BitRevision6`), the AES-256 handler defined by PDF 2.0. This is the default: setting a password is enough.
- AES-256-GCM (`PdfEncryptionAlgorithm.AESGCM`), also defined by PDF 2.0. Choosing it writes the document as PDF 2.0, which older viewers may not open.

A combination that does not exist is adjusted to the nearest one that does: asking for AES with a 40-bit key uses a 128-bit key, and asking for a 256-bit key with RC4 uses AES. The setting that was changed last is kept.

`PdfDocumentSecurity.EncryptMetadata` set to false leaves the XMP metadata packet readable while the content is encrypted. See "Encrypting regulated data" (security-and-signatures.md).

### Code Sample

```csharp
// set document passwords
doc.Security.OwnerPassword = "test1";
doc.Security.UserPassword = "test2";

// set the encryption: AES with a 256-bit key
doc.Security.KeySize = PdfEncryptionKeySize.EncryptKey256Bit;
doc.Security.Algorithm = PdfEncryptionAlgorithm.AES;

//set document permissions
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

' set the encryption: AES with a 256-bit key
doc.Security.KeySize = PdfEncryptionKeySize.EncryptKey256Bit
doc.Security.Algorithm = PdfEncryptionAlgorithm.AES

'set document permissions
doc.Security.CanAssembleDocument = False
doc.Security.CanCopyContent = True
doc.Security.CanEditAnnotations = True
doc.Security.CanEditContent = True
doc.Security.CanFillFormFields = True
doc.Security.CanPrint = True
```

## Advanced security

SelectPdf can be used to set advanced security settings on existing pdf files, manage different permissions and digitally sign the documents.

This can be done using `PdfSecurityManager` object. Using it, the following settings can be customized:

- Owner Password
- User Password
- Encryption Algorithm (RC4, AES or AES-GCM)
- Encryption Key Length (40, 128 or 256 bits; 256 bits as revision 5 or revision 6 of the security handler)
- Whether the XMP metadata is encrypted with the rest of the document
- Assemble Document permission
- Copy Content permission
- Edit Annotations permission
- Edit Content permission
- Fill Form Fields permission
- Print permission (including Full Quality Print)

Digital signatures can be added to an existing PDF document using the `PdfSecurityManager` object. Adding a digital signature requires a PKCS#12 certificate (provided as a .pfx file). The same object validates the signatures a document already carries: see "Digital signatures and signature validation" (security-and-signatures.md), "Validating signatures".

### Encryption Defaults

Setting a password is enough to encrypt a document that is not encrypted yet: it is encrypted with AES using a 256-bit key, revision 6 (`PdfEncryptionKeySize.EncryptKey256BitRevision6`). The document is written as PDF 1.7 and declares the handler as an Adobe extension (level 8), which is how Acrobat writes it; only AES-256-GCM writes the document as PDF 2.0. A document that is already encrypted keeps its own algorithm and key length unless they are changed. Readers older than Acrobat X cannot open AES-256; when such readers must be supported, choose `PdfEncryptionKeySize.EncryptKey128Bit` with `PdfEncryptionAlgorithm.AES` rather than RC4.

`PdfSecurityManager.EncryptMetadata` (true by default) decides whether the XMP metadata stream is encrypted too. Set it to false to leave the metadata readable by a search index or a document management system while the content stays encrypted. The document information entries (title, author, keywords) are encrypted either way, and under a 40-bit RC4 key the metadata is always encrypted. On a document that was encrypted when it was loaded, changing it requires both passwords to be set again. See "Encrypting regulated data" (security-and-signatures.md) for the choices that matter when encrypting regulated data.

### Code Sample

```csharp
// load the pdf document using the advanced security manager
PdfSecurityManager security = new PdfSecurityManager();
security.Load(file);

// encryption algorithm and key length (AES-256, revision 6, is the default)
security.EncryptionAlgorithm = PdfEncryptionAlgorithm.AES;
security.EncryptionKeySize = PdfEncryptionKeySize.EncryptKey256BitRevision6;

// encrypt the XMP metadata too (the default)
security.EncryptMetadata = true;

// set document passwords
security.OwnerPassword = "test1";
security.UserPassword = "test2";

//set document permissions
security.CanAssembleDocument = false;
security.CanCopyContent = true;
security.CanEditAnnotations = true;
security.CanEditContent = true;
security.CanFillFields = true;
security.CanPrint = true;

// add the digital signature
security.Sign(certFile, "password");

// save pdf document
security.Save("Sample.pdf");

// close pdf document
security.Close();
```

```vbnet
	        ' load the pdf document using the advanced security manager
	        Dim security As New PdfSecurityManager()
	        security.Load(file)
	
	        ' encryption algorithm and key length (AES-256, revision 6, is the default)
	        security.EncryptionAlgorithm = PdfEncryptionAlgorithm.AES
	        security.EncryptionKeySize = PdfEncryptionKeySize.EncryptKey256BitRevision6

	        ' encrypt the XMP metadata too (the default)
	        security.EncryptMetadata = True
	
	        ' set document passwords
	        security.OwnerPassword = "test1"
	        security.UserPassword = "test2"
	
	        'set document permissions
	        security.CanAssembleDocument = False
	        security.CanCopyContent = True
	        security.CanEditAnnotations = True
	        security.CanEditContent = True
	        security.CanFillFields = True
	        security.CanPrint = True
	
	        ' add the digital signature
	        security.Sign(certFile, "password")
	
	        ' save pdf document
	        security.Save("Sample.pdf")
	
	        ' close pdf document
	        security.Close()
```

### Digital Signature Overloads

`PdfSecurityManager` exposes five `Sign(...)` overloads. Each overload adds more signature metadata or a visible signature rectangle on top of the minimal one shown above:

- `Sign(certificateFileName, certificatePassword)` - minimal invisible signature.
- `Sign(..., signatureName)` - adds a named signature.
- `Sign(..., signatureName, reason, location, contactInfo)` - adds signer metadata (reason for signing, location, and contact info).
- `Sign(..., signatureName, reason, location, contactInfo, pageIndex, bounds)` - renders a visible signature box on the specified page (zero-based `pageIndex`), inside the supplied `RectangleF` `bounds`.
- `Sign(..., signatureName, reason, location, contactInfo, pageIndex, bounds, timeStampAuthority)` - adds a trusted timestamp from the supplied `TimeStampAuthority` server.

The following sample signs a PDF with full metadata, a visible signature box on the first page, and a TSA-issued timestamp:

```csharp
PdfSecurityManager security = new PdfSecurityManager();
security.Load("contract.pdf");

TimeStampAuthority tsa = new TimeStampAuthority(new Uri("https://tsa.example.com"));
security.Sign(
    "cert.pfx", "pfxpwd",
    "Counter-signed by Example Corp",
    "Contract approval",
    "Amsterdam, NL",
    "legal@example.com",
    0,
    new SelectPdf.Universal.Drawing.RectangleF(400, 40, 150, 50),
    tsa);

security.Save("contract-signed.pdf");
security.Close();
```

```vbnet
Dim security As New PdfSecurityManager()
security.Load("contract.pdf")

Dim tsa As New TimeStampAuthority(New Uri("https://tsa.example.com"))
security.Sign(
    "cert.pfx", "pfxpwd",
    "Counter-signed by Example Corp",
    "Contract approval",
    "Amsterdam, NL",
    "legal@example.com",
    0,
    New SelectPdf.Universal.Drawing.RectangleF(400, 40, 150, 50),
    tsa)

security.Save("contract-signed.pdf")
security.Close()
```

### Trusted Timestamps (RFC 3161)

A trusted timestamp proves that a signed PDF existed in its current form at a specific moment in time, independently of the signer's own clock. SelectPdf attaches a timestamp from an external Time Stamp Authority (TSA) by issuing an RFC 3161 timestamp request over HTTP/HTTPS, then embedding the returned timestamp token inside the signature container. The result is a signature with both an identity claim (your certificate) and a time claim (the TSA's certificate), which is what regulated workflows such as eIDAS / ETSI EN 319 422 expect.

A TSA endpoint is described with a `TimeStampAuthority` object. Pass the resulting instance to the last parameter of the seven-argument `Sign(...)` overload shown above.

- `TimeStampAuthority(Uri tsaServer)` - creates a TSA reference pointing at the supplied RFC 3161 endpoint URL.
- `Server` - the endpoint URL. Use the URL the TSA provider publishes for RFC 3161 requests, not the homepage of the provider.
- `UserName` / `Password` - HTTP Basic credentials sent with the timestamp request, if the TSA requires authentication. Encoded as UTF-8 on the wire, so non-ASCII characters in the credentials are preserved correctly. Leave them unset for anonymous TSAs.
- `TimeOut` - request timeout in seconds. Applies to the connect, write, and read phases of the call to the TSA. Defaults to the framework default if left at zero.

The following sample signs a PDF using an authenticated TSA with a 30-second timeout:

```csharp
PdfSecurityManager security = new PdfSecurityManager();
security.Load("contract.pdf");

TimeStampAuthority tsa = new TimeStampAuthority(
    new Uri("https://tsa.example.com/rfc3161"));
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
    new SelectPdf.Universal.Drawing.RectangleF(400, 40, 150, 50),
    tsa);

security.Save("contract-signed.pdf");
security.Close();
```

```vbnet
Dim security As New PdfSecurityManager()
security.Load("contract.pdf")

Dim tsa As New TimeStampAuthority(
    New Uri("https://tsa.example.com/rfc3161"))
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
    New SelectPdf.Universal.Drawing.RectangleF(400, 40, 150, 50),
    tsa)

security.Save("contract-signed.pdf")
security.Close()
```

The resulting PDF carries a single Adobe.PPKLite signature whose CMS signed-data container embeds the RFC 3161 timestamp token as an unsigned attribute. Acrobat, Reader, and other compliant validators will display the signature as "signed and time-stamped" and surface the TSA-asserted time alongside the signer identity.

### Timestamp Error Handling

The TSA call happens at `Save` time, after the document has been signed in memory but before the file is written. If the TSA cannot be reached or refuses to issue a token, `Save` throws and no signed file is produced. The most common failure cases:

- **Network failure** - DNS resolution failure, connection refused, TLS handshake failure, or proxy interception. The thrown exception preserves the underlying network error as its inner exception, so the original cause is visible to the caller.
- **HTTP error response** - the TSA returned a non-200 status code. The exception message includes the HTTP status code (for example, `HTTP 401 Unauthorized` when credentials are required but were not supplied).
- **TSA rejection** - the TSA responded successfully at the HTTP layer but refused to issue a token (RFC 3161 PKI status 2 "rejection", 3 "waiting", 4 "revocationWarning", or 5 "revocationNotification"). The exception message names the status code and label. Typical causes are quota exhaustion, an unauthorized client certificate, or a TSA-side outage.
- **Timeout** - the TSA accepted the connection but did not finish responding within the configured `TimeOut` window.

Because all four cases throw before any file is written, the application can safely catch the exception, switch to a backup TSA, and retry without producing a partial or unsigned output.

## Encrypting regulated data

Regulations that cover personal, medical or financial data - HIPAA, GDPR, PCI DSS - do not certify file formats or libraries. Where they ask for encryption they point to current guidance, which names AES (NIST SP 800-111 for stored data). This topic says how to produce pdf documents that meet that bar, and what pdf encryption does and does not protect.

### AES-256 is the default

Setting a password is enough: the document is encrypted with AES and a 256-bit key (revision 6 of the pdf security handler, `PdfEncryptionKeySize.EncryptKey256BitRevision6` with `PdfEncryptionAlgorithm.AES`). This applies to a new document, to an existing document that is not encrypted, and to the html to pdf converter's `HtmlToPdfOptions.SecurityOptions`. A document that is already encrypted keeps its own settings. The document is written as PDF 1.7 and declares the handler as an Adobe extension (level 8), which is how Acrobat writes it; only AES-256-GCM writes the document as PDF 2.0.

Every current pdf viewer opens these documents. Viewers older than Adobe Acrobat X (2010) do not; if you must support them, choose AES with a 128-bit key rather than RC4.

An existing document is encrypted the same way with `PdfSecurityManager`, whose `PdfSecurityManager.EncryptionKeySize`, `PdfSecurityManager.EncryptionAlgorithm` and `PdfSecurityManager.EncryptMetadata` have the same defaults.

### What to avoid

- RC4, with a 40-bit or a 128-bit key. It is broken and is supported only for old viewers.
- An owner password without a user password. Anyone can then open the document; the permissions (printing, copying, editing) are honoured by compliant viewers, but they are not what keeps the content confidential. The user password is.
- Passwords written into source code or configuration files in plain text.

AES-256-GCM (`PdfEncryptionAlgorithm.AESGCM`) is available as well. It is defined by PDF 2.0, so choosing it writes the document as PDF 2.0, which only recent viewers open.

### Metadata

By default everything is encrypted. Set `PdfDocumentSecurity.EncryptMetadata` (or `PdfSecurityOptions.EncryptMetadata` for a conversion) to false to leave the document's XMP metadata packet readable - by a search index or a document management system - while the content stays encrypted. Leave it true when the metadata itself identifies a person: a title such as a patient's name is personal data too. The document information entries (title, author, keywords) are encrypted either way.

On a document that is already encrypted, the metadata encryption can be changed only when both passwords are set again, because the encryption keys depend on it.

### Archiving

PDF/A does not allow encryption, and a PDF/A document with a password is refused. Protect an archived document at the storage level instead.

### Code Sample

```csharp
// html to pdf: a password is enough for AES-256
HtmlToPdf converter = new HtmlToPdf();
converter.Options.SecurityOptions.UserPassword = userPassword;
converter.Options.SecurityOptions.OwnerPassword = ownerPassword;
converter.Options.SecurityOptions.CanCopyContent = false;
PdfDocument doc = converter.ConvertUrl(url);
doc.Save(file);
doc.Close();

// an existing document
PdfDocument existing = new PdfDocument(existingFile);
existing.Security.UserPassword = userPassword;
existing.Security.OwnerPassword = ownerPassword;
existing.Save(protectedFile);
existing.Close();
```

```vbnet
' html to pdf: a password is enough for AES-256
Dim converter As New HtmlToPdf()
converter.Options.SecurityOptions.UserPassword = userPassword
converter.Options.SecurityOptions.OwnerPassword = ownerPassword
converter.Options.SecurityOptions.CanCopyContent = False
Dim doc As PdfDocument = converter.ConvertUrl(url)
doc.Save(file)
doc.Close()

' an existing document
Dim existing As New PdfDocument(existingFile)
existing.Security.UserPassword = userPassword
existing.Security.OwnerPassword = ownerPassword
existing.Save(protectedFile)
existing.Close()
```

## Digital signatures and signature validation

Digital signatures can be added to a PDF document using `PdfDigitalSignatureElement` objects. Adding a digital signature requires a PKCS#12 certificate (provided as a .pfx or a .p12 file).

A certificate read from a file carries its private key, which the operating system keeps until the certificate is released - on Windows as a key file in the user profile, on macOS in a temporary keychain. Dispose of the `PdfDigitalCertificatesCollection` (and of a `PdfCertificateStoreSigner`) with a `using` statement, AFTER the documents it signs are saved: a document is signed when it is saved, and a certificate disposed before that fails the save. `PdfSecurityManager` releases the certificates its `Sign` loads when it is closed or disposed.

### Long-term validation (LTV)

A signature can be validated only as long as its certificate can be checked: once the certificate has expired, or the issuer's revocation services are gone, a validator has nothing to go on. Set `PdfDigitalSignatureElement.EnableLtv` (or `PdfSecurityManager.EnableLtv` before `Sign`) and the revocation data of the signing certificate and of its issuers - an OCSP response or a certificate revocation list for each, from the addresses the certificates name - is embedded in the document when it is saved, together with the issuer certificates (PAdES B-LT). The signature can then be validated offline, years later.

The data is added in a revision of its own after the signature, so the signature still covers everything it signed, and each signer of a document signed in turn adds the data for their own signature. The issuer certificates are taken from the certificate file the signing certificate came from and from the system certificate stores. Saving fails when a certificate of the chain names no OCSP responder or revocation list, or when they cannot be reached, so a document is never handed back without the data it was asked to carry. `PdfSignatureValidationResult.IsLtvEnabled` reports whether a signature has it.

```csharp
using PdfDigitalCertificatesCollection certs =
    PdfDigitalCertificatesStore.GetCertificates("signer.pfx", "password");
PdfDigitalCertificate signer = certs.GetCertBySubject("Contract Signer");

using PdfDocument document = new PdfDocument("contract.pdf");
PdfDigitalSignatureElement signature = new PdfDigitalSignatureElement(
    new SelectPdf.Universal.Drawing.RectangleF(40, 600, 240, 70), signer);
signature.Reason = "Approved";
signature.EnableLtv = true;
document.Pages[0].Add(signature);
document.Save("contract-signed.pdf");
```

```vbnet
Using certs As PdfDigitalCertificatesCollection =
        PdfDigitalCertificatesStore.GetCertificates("signer.pfx", "password")
    Dim signer As PdfDigitalCertificate = certs.GetCertBySubject("Contract Signer")

    Using document As New PdfDocument("contract.pdf")
        Dim signature As New PdfDigitalSignatureElement(
            New SelectPdf.Universal.Drawing.RectangleF(40, 600, 240, 70), signer)
        signature.Reason = "Approved"
        signature.EnableLtv = True
        document.Pages(0).Add(signature)
        document.Save("contract-signed.pdf")
    End Using
End Using
```

### Signing a form's signature field

A form often arrives with an empty signature field - a "sign here" box. Pass that field's name to `Sign` and the signature goes into it, on the field's own page and in its own rectangle (the page index and rectangle given to `Sign` are then not used). A field that is already signed is never overwritten: a new signature with the same name gets a field of its own.

```csharp
PdfSecurityManager manager = new PdfSecurityManager();
manager.Load("form.pdf");
manager.Sign("signer.pfx", "password", "Approver",
    "Approved", "Bucharest", "office@example.com");
manager.Save("form-signed.pdf");
manager.Close();
```

```vbnet
Dim manager As New PdfSecurityManager()
manager.Load("form.pdf")
manager.Sign("signer.pfx", "password", "Approver",
    "Approved", "Bucharest", "office@example.com")
manager.Save("form-signed.pdf")
manager.Close()
```

### Hash algorithm and key types

Signatures are computed with SHA-256 by default. Set `PdfDigitalSignatureElement.DigestAlgorithm` (or `PdfSecurityManager.DigestAlgorithm` before `Sign`) to `PdfSignatureDigest.SHA384` or `PdfSignatureDigest.SHA512` when a signature policy asks for them. Both RSA and elliptic-curve (ECDSA) certificates can sign; SHA-384 is the usual pairing for a P-384 key.

```csharp
PdfDigitalSignatureElement signature = new PdfDigitalSignatureElement(
    new SelectPdf.Universal.Drawing.RectangleF(40, 600, 240, 70), signer);
signature.DigestAlgorithm = PdfSignatureDigest.SHA384;
document.Pages[0].Add(signature);
```

```vbnet
Dim signature As New PdfDigitalSignatureElement(
    New SelectPdf.Universal.Drawing.RectangleF(40, 600, 240, 70), signer)
signature.DigestAlgorithm = PdfSignatureDigest.SHA384
document.Pages(0).Add(signature)
```

### Signing with a smart card, a USB token or an HSM

Two ready-made signers cover keys that never leave a device. `PdfCertificateStoreSigner` signs with a certificate from the system certificate store, found by its thumbprint - which is where the middleware of a smart card or USB token registers the device's certificate on Windows. The device signs and asks for its PIN itself. `PdfPkcs11Signer` talks to the device directly through its vendor's PKCS#11 library: an HSM, a token on a server, or any device on Linux and macOS. It reads the signing certificate from the token, logs in with the PIN and keeps its session open until it is disposed, so one signer signs any number of documents. Both find the issuer certificates and embed them, and both work with `PdfSecurityManager.EnableLtv`, a time-stamp authority and the visible signature element.

```csharp
// A certificate on a USB token, as the certificate manager lists it.
using (var storeSigner = new PdfCertificateStoreSigner(
    "BABEA413778B70C92E266D2458E193BD6E62A6E3"))
using (var manager = new PdfSecurityManager())
{
    manager.Load("contract.pdf");
    manager.Sign(storeSigner, "Signature1", "Approved", "Bucharest", "",
        0, new RectangleF(40, 120, 260, 70));
    manager.Save("contract-signed.pdf");
}

// The same token, or an HSM, through the vendor's PKCS#11 library.
using (var pkcs11Signer = new PdfPkcs11Signer(
    @"C:\Windows\System32\eTPKCS11.dll", "1234", "My Token", "Signing key"))
{
    PdfDocument doc = new PdfDocument("invoice.pdf");
    doc.Pages[0].Add(new PdfDigitalSignatureElement(
        new RectangleF(40, 700, 260, 70), pkcs11Signer));
    doc.Save("invoice-signed.pdf");
    doc.Close();
}
```

```vbnet
' A certificate on a USB token, as the certificate manager lists it.
Using storeSigner As New PdfCertificateStoreSigner(
        "BABEA413778B70C92E266D2458E193BD6E62A6E3"),
      manager As New PdfSecurityManager()
    manager.Load("contract.pdf")
    manager.Sign(storeSigner, "Signature1", "Approved", "Bucharest", "",
        0, New RectangleF(40, 120, 260, 70))
    manager.Save("contract-signed.pdf")
End Using

' The same token, or an HSM, through the vendor's PKCS#11 library.
Using pkcs11Signer As New PdfPkcs11Signer(
    "C:\Windows\System32\eTPKCS11.dll", "1234", "My Token", "Signing key")
    Dim doc As New PdfDocument("invoice.pdf")
    doc.Pages(0).Add(New PdfDigitalSignatureElement(
        New RectangleF(40, 700, 260, 70), pkcs11Signer))
    doc.Save("invoice-signed.pdf")
    doc.Close()
End Using
```

The PKCS#11 library must match the application's processor: a 64-bit application needs the vendor's 64-bit library. With no token label exactly one token must be present, and with no key label the token must hold exactly one private key - the signer never tries the PIN on a token it was not pointed at, since a wrong PIN counts against the token's retry limit.

### The visible signature's appearance

A visible signature shows the signer's name large on the left and, on the right, "Digitally signed by", the name, the reason and location when given, and the date with its UTC offset. Behind the text a faint red handwritten flourish is drawn. Replace it with your own image - a company logo, a seal, a scanned handwritten signature - with `PdfDigitalSignatureElement.SignatureBackgroundImage` (the same property exists on `PdfSecurityManager`), and set how strongly it is drawn with `PdfDigitalSignatureElement.SignatureBackgroundOpacity`, from 0 (no background) to 1 (the image's own colours; default 0.25). The background is lightened rather than made transparent, so it is allowed in every PDF/A and PDF/X document.

```csharp
var signature = new PdfDigitalSignatureElement(
    new RectangleF(40, 700, 260, 70), certificate);
signature.SignatureBackgroundImage = File.ReadAllBytes("logo.png");
signature.SignatureBackgroundOpacity = 0.3f;
page.Add(signature);
```

```vbnet
Dim signature As New PdfDigitalSignatureElement(
    New RectangleF(40, 700, 260, 70), certificate)
signature.SignatureBackgroundImage = File.ReadAllBytes("logo.png")
signature.SignatureBackgroundOpacity = 0.3F
page.Add(signature)
```

### Signing with a key held outside: HSMs, smart cards and key services

When the private key lives in a hardware security module, a smart card, a PKCS#11 token or a cloud key service, it cannot be handed to the library as a certificate file. Implement `IPdfExternalSigner` instead: it gives the signer's certificates (DER-encoded, the signing certificate first, then its issuers) and signs one `PdfExternalSigningRequest`. The library builds the whole signature - certificates, signed attributes, timestamp, long-term validation data - and asks the signer only for the raw signature value, so the key never leaves the device or service. Pass the signer to `Sign` or to a `PdfDigitalSignatureElement`.

The request carries the data to sign and its digest: sign the data with a service that hashes it itself, or the digest with one that expects a hash. An RSA key returns a PKCS#1 v1.5 signature (RSA-PSS is not supported); an ECDSA key may return the DER-encoded signature or r and s concatenated, the form .NET and most key services produce. The value is checked against the signing certificate before it is written, so a value from another key or another hash algorithm makes the save fail instead of producing a document that looks signed and does not verify. `PdfSecurityManager.DigestAlgorithm`, `PdfSecurityManager.EnableLtv`, a time-stamp authority and signing a form's named field work as they do with a certificate file.

```csharp
// Any key that can sign a digest: here a .NET RSA object, which HSM
// and key service SDKs commonly provide.
class KeySigner : IPdfExternalSigner
{
    private readonly RSA key;

    public KeySigner(IList<byte[]> certificates, RSA key)
    {
        Certificates = certificates;
        this.key = key;
    }

    public IList<byte[]> Certificates { get; }

    public byte[] Sign(PdfExternalSigningRequest request)
    {
        var hash = new HashAlgorithmName(request.DigestAlgorithm.ToString());
        return key.SignHash(request.Digest, hash, RSASignaturePadding.Pkcs1);
    }
}

PdfSecurityManager manager = new PdfSecurityManager();
manager.Load("contract.pdf");
manager.Sign(new KeySigner(certificates, key), "Signature1");
manager.Save("contract-signed.pdf");
manager.Close();
```

```vbnet
' Any key that can sign a digest: here a .NET RSA object, which HSM
' and key service SDKs commonly provide.
Class KeySigner
    Implements IPdfExternalSigner

    Private ReadOnly key As RSA
    Private ReadOnly chain As IList(Of Byte())

    Public Sub New(certificates As IList(Of Byte()), key As RSA)
        chain = certificates
        Me.key = key
    End Sub

    Public ReadOnly Property Certificates As IList(Of Byte()) _
        Implements IPdfExternalSigner.Certificates
        Get
            Return chain
        End Get
    End Property

    Public Function Sign(request As PdfExternalSigningRequest) As Byte() _
        Implements IPdfExternalSigner.Sign
        Dim hash As New HashAlgorithmName(request.DigestAlgorithm.ToString())
        Return key.SignHash(request.Digest, hash, RSASignaturePadding.Pkcs1)
    End Function
End Class

Dim manager As New PdfSecurityManager()
manager.Load("contract.pdf")
manager.Sign(New KeySigner(certificates, key), "Signature1")
manager.Save("contract-signed.pdf")
manager.Close()
```

**Signing in two steps.** A remote signing service where the signer approves on another device, or a smart card in a user's browser, cannot answer while the document is being saved. `PrepareExternalSignature` gives the document a signature field with an empty, reserved signature and returns the request; save the prepared document as it is, have the request signed, then `CompleteExternalSignature` writes the value into it, with the same signature name, certificates and hash algorithm. The two steps can run in different processes, hours apart. The completed document is validated before it is returned. Long-term validation and a time-stamp authority are not available in this mode.

```csharp
// step one: reserve the signature and hand out what must be signed
PdfSecurityManager manager = new PdfSecurityManager();
manager.Load("contract.pdf");
PdfExternalSigningRequest request =
    manager.PrepareExternalSignature(certificates, "Signature1");
manager.Save("contract-prepared.pdf");
manager.Close();

byte[] value = remoteService.SignDigest(request.Digest);

// step two, later: write the value into the prepared document
PdfSecurityManager.CompleteExternalSignature("contract-prepared.pdf",
    "contract-signed.pdf", "Signature1", certificates,
    PdfSignatureDigest.SHA256, value);
```

```vbnet
' step one: reserve the signature and hand out what must be signed
Dim manager As New PdfSecurityManager()
manager.Load("contract.pdf")
Dim request As PdfExternalSigningRequest =
    manager.PrepareExternalSignature(certificates, "Signature1")
manager.Save("contract-prepared.pdf")
manager.Close()

Dim value As Byte() = remoteService.SignDigest(request.Digest)

' step two, later: write the value into the prepared document
PdfSecurityManager.CompleteExternalSignature("contract-prepared.pdf",
    "contract-signed.pdf", "Signature1", certificates,
    PdfSignatureDigest.SHA256, value)
```

### Validating signatures

`PdfSecurityManager.ValidateSignatures` checks every signature of a loaded document and returns a `PdfSignatureValidationResult` for each: whether it is intact (`PdfSignatureValidationResult.IsValid`), whether its certificate chains to a trusted root (`PdfSignatureValidationResult.IsTrusted`), whether content was added to the document after it (`PdfSignatureValidationResult.CoversWholeDocument`), the signing and time-stamp times, the signer and the certificate, and a `PdfSignatureValidationResult.Status` that sums them up. Pass your own certificate authority to trust it as a root, and set `PdfSecurityManager.RevocationCheckEnabled` to ask the certificate's issuer whether it has been revoked.

Each signature is checked against the bytes it was made over, so a signature followed by a later one, or by a form fill, is reported intact.

With the revocation check on, the revocation data a document carries for long-term validation is used first, so an LTV-enabled signature is checked without going online. An OCSP response or revocation list counts only when the certificate's issuer (or a responder it authorised) signed it.

```csharp
PdfSecurityManager manager = new PdfSecurityManager();
manager.Load("contract-signed.pdf");
manager.RevocationCheckEnabled = true;

// trust the organisation's own certificate authority as well as the system's
using PdfDigitalCertificatesCollection roots =
    PdfDigitalCertificatesStore.GetCertificates("our-ca.pfx", "password");
foreach (PdfSignatureValidationResult result in manager.ValidateSignatures(roots))
{
    Console.WriteLine("{0}: {1}, signed by {2} on {3}",
        result.SignatureName, result.Status, result.SignerName, result.SigningTime);
    foreach (string message in result.Messages)
        Console.WriteLine("  " + message);
}
manager.Close();
```

```vbnet
Dim manager As New PdfSecurityManager()
manager.Load("contract-signed.pdf")
manager.RevocationCheckEnabled = True

' trust the organisation's own certificate authority as well as the system's
Dim results As PdfSignatureValidationResult()
Using roots As PdfDigitalCertificatesCollection =
        PdfDigitalCertificatesStore.GetCertificates("our-ca.pfx", "password")
    results = manager.ValidateSignatures(roots)
End Using
For Each result As PdfSignatureValidationResult In results
    Console.WriteLine("{0}: {1}, signed by {2} on {3}",
        result.SignatureName, result.Status, result.SignerName, result.SigningTime)
    For Each message As String In result.Messages
        Console.WriteLine("  " + message)
    Next
Next
manager.Close()
```

### Sample Code

The following sample code shows how to add a digital signature to a pdf document using Select.Pdf Library for .NET.

```csharp
	// create a new pdf document
    PdfDocument doc = new PdfDocument();

    // add a new page to the document
    PdfPage page = doc.AddPage();

    // get image path 
    // the image will be used to display the digital signature over it
    string imgFile = @"C:\Path\to\logo.png";

    // get certificate path
    string certFile = @"C:\Path\to\certificate.pfx";

    // define a rendering result object
    PdfRenderingResult result;

    // create image element from file path 
    PdfImageElement img = new PdfImageElement(0, 0, imgFile);
    result = page.Add(img);

    // get the #PKCS12 certificate from file; dispose of it after the save,
    // since the document is signed when it is saved
    using PdfDigitalCertificatesCollection certificates =
        PdfDigitalCertificatesStore.GetCertificates(certFile, "selectpdf");
    PdfDigitalCertificate certificate = certificates[0];

    // create the digital signature object
    PdfDigitalSignatureElement signature = 
        new PdfDigitalSignatureElement(result.PdfPageLastRectangle, certificate);
    signature.Reason = "SelectPdf";
    signature.ContactInfo = "SelectPdf";
    signature.Location = "SelectPdf";
    page.Add(signature);

    // save pdf document
    doc.Save("Sample.pdf");

    // close pdf document
    doc.Close();
```

```vbnet
        	' create a new pdf document
	        Dim doc As New PdfDocument()
	
	        ' add a new page to the document
	        Dim page As PdfPage = doc.AddPage()
	
	        ' get image path 
	        ' the image will be used to display the digital signature over it
	        Dim imgFile As String = "C:\Path\to\logo.png"
	
	        ' get certificate path
	        Dim certFile As String = "C:\Path\to\certificate.pfx"
	
	        ' define a rendering result object
	        Dim result As PdfRenderingResult
	
	        ' create image element from file path 
	        Dim img As New PdfImageElement(0, 0, imgFile)
	        result = page.Add(img)
	
	        ' get the #PKCS12 certificate from file; dispose of it after the save,
	        ' since the document is signed when it is saved
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
	
	        ' save pdf document
	        doc.Save("Sample.pdf")
	
	        ' close pdf document and release the certificates
	        doc.Close()
	        certificates.Dispose()
```
