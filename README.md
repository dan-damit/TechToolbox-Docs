# TechToolbox-Docs

Public documentation site for the **TechToolbox** PowerShell module.

This repository hosts the Markdown help pages used by the module’s `-Online` help experience.

## What is this?

TechToolbox includes commands that support `Get-Help <Command> -Online`. When you use `-Online`, TechToolbox opens the matching page in this repo.

## Docs structure

- Each command’s online help is a standalone Markdown file (e.g., `New-OnPremUserFromTemplate` → `user-provisioning.md`).
- Pages follow a help-friendly layout: **Synopsis**, **Description**, **Parameters**, **Examples**, **Notes**, and **Related Links**.

## Contributing

Issues and pull requests are welcome.

If you’re adding a new page:

1. Create a new `*.md` file.
2. Use the same section layout as existing pages.
3. Keep command names and parameter names exact.

## Publishing (GitHub Pages)

GitHub Pages can be enabled for this repo to make browsing easier:

- **Settings → Pages**
- Source: **Deploy from a branch**
- Folder: **/ (root)**

## License

MIT License

Copyright (c) 2026 Dan.Damit

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
