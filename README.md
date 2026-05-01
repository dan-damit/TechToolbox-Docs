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

Add a `LICENSE` file if/when you decide on one.
