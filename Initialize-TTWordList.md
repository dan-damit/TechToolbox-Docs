# Initialize-TTWordList

## SYNOPSIS
Initializes a word list file with a curated set of words.

## SYNTAX

### Default (Default)
```powershell
Initialize-TTWordList [-Path <String>]
```

### NoAmbiguous
```powershell
Initialize-TTWordList [-Path <String>] [-NoAmbiguous]
```

## DESCRIPTION
The Initialize-TTWordList cmdlet creates a word list file containing a curated starter list of words. This is typically used to set up an initial configuration for spell-checking or other text processing features within TechToolbox.

By default, the word list includes common English words. If the `-NoAmbiguous` switch is specified, the function filters out any words that contain ambiguous characters (specifically 'i', 'l', and 'o') to reduce confusion in certain contexts.

## PARAMETERS

### -Path <String>
The file path where the word list will be written.

- **Required?** false
- **Position?** named
- **Default value:** `<TechToolboxModuleRoot>\Config\wordlist.txt`
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

### -NoAmbiguous <Switch>
If specified, filters out words containing the letters 'i', 'l', or 'o'.

- **Required?** false
- **Position?** named
- **Default value:** False
- **Accept pipeline input?** false
- **Accept wildcard characters?** false

## EXAMPLES

### Example 1
```powershell
Initialize-TTWordList
```
**Description:**
Initializes the word list file at the default location (`<TechToolboxModuleRoot>\Config\wordlist.txt`) with the standard curated set of words.

### Example 2
```powershell
Initialize-TTWordList -Path 'C:\custom\path\wordlist.txt' -NoAmbiguous
```
**Description:**
Initializes a word list file at `C:\custom\path\wordlist.txt` and filters out words containing ambiguous characters ('i', 'l', 'o').