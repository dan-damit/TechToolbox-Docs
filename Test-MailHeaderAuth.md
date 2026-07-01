# Test-MailHeaderAuth

## SYNOPSIS
Analyzes email headers to determine authentication status and phishing risk.

## SYNTAX

### Text (Default)
```powershell
Test-MailHeaderAuth [-HeadersText] <String> [[-Format] <String>] [-AsObject]
```

### File
```powershell
Test-MailHeaderAuth -Path <String> [[-Format] <String>] [-AsObject]
```

### Clipboard
```powershell
Test-MailHeaderAuth [-FromClipboard] [[-Format] <String>] [-AsObject]
```

## DESCRIPTION
The `Test-MailHeaderAuth` cmdlet analyzes raw email headers to determine the authentication status of a message. It checks for SPF, DKIM, DMARC, and ARC authentication results.

It provides a verdict on message legitimacy based on authentication alignment. This is useful for identifying potential phishing attempts or spoofed emails by verifying that the sender's domain has authorized the sending server.

## PARAMETERS

### -HeadersText <String>
The raw email headers as a string.

- **Required?** True (in Text parameter set)
- **Position?** 1
- **Default value:** None
- **Accept pipeline input?** False
- **Accept wildcard characters?** False

### -Path <String>
The path to a file containing email headers.

- **Required?** True (in File parameter set)
- **Position?** Named
- **Default value:** None
- **Accept pipeline input?** False
- **Accept wildcard characters?** False

### -FromClipboard [Switch]
Reads the email headers directly from the system clipboard. This is only available on Windows systems.

- **Required?** True (in Clipboard parameter set)
- **Position?** Named
- **Default value:** None
- **Accept pipeline input?** False
- **Accept wildcard characters?** False

### -Format <String>
The output format for the results.

- **Valid values:**
  - `Summary`: Provides a human-readable summary of the authentication status (default).
  - `Markdown`: Returns the results in Markdown format.
  - `Object`: Returns a PowerShell custom object containing all details.
  - `Json`: Returns the results as a JSON string.

- **Required?** False
- **Position?** 2
- **Default value:** Summary
- **Accept pipeline input?** False
- **Accept wildcard characters?** False

### -AsObject [Switch]
Forces the cmdlet to return a PowerShell object (`PSCustomObject`) regardless of the `-Format` setting. This is useful for further programmatic processing.

- **Required?** False
- **Position?** Named
- **Default value:** None
- **Accept pipeline input?** False
- **Accept wildcard characters?** False

## INPUTS
None. You cannot pipe objects to this cmdlet.

## OUTPUTS
`PSCustomObject` or formatted string (depending on the `-Format` parameter).

When using `Summary`, a text block is returned. When using `Markdown`, a Markdown string is returned. When using `Object` or `AsObject`, a `PSCustomObject` is returned containing properties such as:
- SPFResult
- DKIMResult
- DMARCResult
- ARCResult
- Verdict (e.g., Pass, Fail, Warning)
- Details regarding alignment and policy.

## EXAMPLES

### Example 1: Analyze headers from a string
```powershell
$headers = "Received: from mail.example.com\r\nSPF: pass\r\nDKIM-Signature: v=1; ..."
Test-MailHeaderAuth -HeadersText $headers
```
This command analyzes the raw email headers provided in the `$headers` variable and outputs a summary of the authentication status.

### Example 2: Analyze headers from a file
```powershell
Test-MailHeaderAuth -Path "C:\Temp\email_headers.txt" -Format Markdown
```
This command reads email headers from `email_headers.txt` and returns the analysis in Markdown format.

### Example 3: Analyze headers from clipboard as JSON
```powershell
Test-MailHeaderAuth -FromClipboard -Format Json
```
This command reads the current content of the clipboard, assuming it contains email headers, and outputs the authentication results as a JSON string.

## NOTES
- The cmdlet requires appropriate permissions to access the clipboard on Windows if `-FromClipboard` is used.
- Authentication results depend on the presence of specific header fields (e.g., `Authentication-Results`, `Received-Spf`). If these are missing, the result may be 'None' or 'Neutral'.

## SEE ALSO
- [SPF](https://en.wikipedia.org/wiki/Sender_Policy_Framework)
- [DKIM](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)
- [DMARC](https://en.wikipedia.org/wiki/DMARC)
