# Changelog

All notable changes to DevArt Documents are documented in this file.

The format is based on Keep a Changelog.

## [1.1.1] - 2026-09-05

Production patch for Joomla 6 / PHP 8.3+. Reported fixes and small safe additions only; no breaking changes to storage paths, ACL, or routing defaults.

### Added

- Category option **Embedded PDF preview for this category** (documents / show all / hide all) overriding the per-document toggle
- Own DevArt Documents tags: Tag Manager, document assignment, frontend tag pages, and tag filters for menus and the module (Joomla core tags no longer used)
- Module content scope **Single document** with searchable DocumentSelect modal picker (Events-aligned) and document detail layout rendering

### Fixed

- ZIP and other package types (docx, xlsx, pptx, rar, 7z, …) upload again via Media Manager; install/update merges Documents defaults into `com_media` document/restrict/MIME lists
- Multiple tags can be created and assigned again after removing the short-lived Joomla core tags content-type registration
- Document detail opens when Routing is Off with no Documents menu (non-home layout Itemid so Gantry Page Content is not suppressed)
- Web asset registry paths corrected (`css/` / `js/` no longer duplicated), restoring DocumentSelect picker assets
- Category search submit button unlocks again when anti-spam is active (`site-search.js` path fixed)
- Search results count shows the full filtered total instead of the current page size
- Media Manager / article image browse no longer sticks on DevArt Documents storage; Documents filesystem provider registers only for the document file picker (or an explicit Documents adapter path)

### Notes

- Requires Joomla 6.0+ and PHP 8.3.0+
- Install and update only through `pkg_devartdocuments`

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
