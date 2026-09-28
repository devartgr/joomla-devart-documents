# Changelog

All notable changes to DevArt Documents are documented in this file.

The format is based on Keep a Changelog.

## [1.1.3] - 2026-09-28

Bugfix for PHP 8.x Undefined property warnings when resolving Documents menu routes. No changes to storage paths, ACL, routing defaults, or the database schema.

### Fixed

- Frontend menu lookup no longer filters by `MenuItem::client_id`. Joomla `SiteMenu` already loads only site items and does not expose `client_id` on `MenuItem`, so `getItems(..., 'client_id')` triggered `Undefined property` warnings (GitHub issue #1)

### Notes

- Requires Joomla 6.0+ and PHP 8.3.0+
- Install and update only through `pkg_devartdocuments`

## [1.1.2] - 2026-09-24

Compatibility patch for Joomla 6 / PHP 8.3+ that prepares the codebase for Joomla 7 and PHP 8.5. No changes to storage paths, ACL, routing or the database schema.

### Fixed

- Dashboard storage protection probe reports the real HTTP status again (the HTTP factory was called statically and always reported status 0)
- Joomla 7 readiness: removed APIs scheduled for removal in 7.0 (`Factory::getConfig`, `Factory::getCache`, application `input` property, `Categories::getInstance`, `Table::getDbo`, view `get()`, Document `addScript` / `addScriptDeclaration` / `addStyleDeclaration`)
- Joomla 7 readiness for plugins: plugins no longer receive the event dispatcher in their constructor, and the system plugin reads form and save events through typed event getters instead of numeric event arguments
- Google Drive and OAuth requests use the Joomla framework HTTP client (PSR-7) instead of the deprecated CMS HTTP client, keeping the Joomla user agent and Global Configuration proxy settings
- Frontend styles and scripts load through the WebAssetManager in views, the module and the content plugin; feed and raw documents no longer attempt to load HTML assets
- Administrator forms and lists load form validation, keepalive and multiselect through the WebAssetManager
- PHP 8.5 readiness: removed deprecated `curl_close()` / `finfo_close()` calls and `$http_response_header` in the anti-spam verification fallback
- Database queries use `createQuery()` and `setLimit()` instead of the deprecated `getQuery(true)` and `setQuery()` offset/limit arguments
- Search session values are removed with `Session::remove()` instead of the deprecated `Session::clear($key)`

### Notes

- Requires Joomla 6.0+ and PHP 8.3.0+
- Install and update only through `pkg_devartdocuments`

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
