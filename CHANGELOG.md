# Changelog

All notable changes to DevArt Documents are documented in this file.

The format is based on Keep a Changelog.

## [1.1.0] - 2026-08-29

First major public update after `1.0.1`. Internal development builds `1.0.2`–`1.0.38` are folded into this release and are not published separately.

### Added

- Software display template with optional Changelog, Releases, Documentation, Requirements, Hash, and custom Download label fields
- PDF display template with ACL-gated embedded preview, document version, effective date, and print control
- Editor button **DevArt Embed PDF** (PDF-only picker) inserting `{devartdocument id="N" layout="embed" height="…"}`
- Content shortcode embed layout for ACL-gated inline PDF preview in articles
- Per-extension Open overrides in Tools → Settings
- External URL storage driver (http/https) with ACL-gated redirect open/download
- Document Ordering field for Manual ordering on category menus and modules
- Category and menu display/theme overrides with documented resolution chains
- Shared `DocumentEmbedService` and common embed template

### Changed

- Theme resolution: listing cards use document → menu → category; document detail ignores menu overrides
- Installer admin submenu link normalization reports only when a link actually changes
- PHP file headers aligned to GPLv3+; root `LICENSE.txt` included
- Extension manifests include `<description>` for module and plugins that were missing it

### Fixed

- Software/PDF Yes toggles correctly reveal dependent URL, hash, and related input fields
- GitHub help text hidden when the PDF template is selected
- Content plugin loads `com_devartdocuments` language so article shortcodes show translated button labels
- Google Drive token vault and OAuth hardening (HKDF, host checks, redirect allowlist)
- Archive MIME handling without treating `application/octet-stream` as a safe open type

### Security

- Encrypted Google Drive OAuth token storage retained (base64 used only as encoding for ciphertext — not obfuscation)
- Embed iframe always targets the ACL-gated `document.open` endpoint

## [1.0.1] - 2026-08-10

First public release for Joomla 6, with a critical clean-site installation fix.

### Fixed

- Clean-site package installation failures caused by incomplete schema creation before install SQL
- Installer postflight errors when component classes were not yet booted
- Missing `idx_folder_batch_id` detection on fresh installations
