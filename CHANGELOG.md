# Changelog

All notable changes to DevArt Documents are documented in this file.

The format is based on Keep a Changelog.

## [1.1.5] - 2026-10-08

Installer hotfix after 1.1.4. No changes to storage paths, ACL, routing defaults, or document data.

### Fixed

- Update/install from 1.1.3 (and failed 1.1.4 attempts) no longer stops with `JInstaller: :Install: Error SQL` / Duplicate column `scan_cursor`. Cause: `script.php` preflight already added the column, then `1.1.4.sql` ran a non-idempotent `ALTER TABLE … ADD`. `1.1.4.sql` is now a schema marker only; the column remains ensured idempotently in `script.php` (additive, downgrade-friendly)

### Notes

- Requires Joomla 6.0+ and PHP 8.3.0+
- Install and update only through `pkg_devartdocuments`
- Sites that failed on 1.1.4 can update directly to 1.1.5

## [1.1.4] - 2026-10-04

Correctness, security, and performance patch after 1.1.3 for Joomla 6 / PHP 8.3+. No changes to storage paths, ACL, or routing defaults. Folder-batch import adds optional `scan_cursor` checkpoint column (empty by default).

### Fixed

- HTTP Range parsing rejects invalid `Range` headers correctly (`HttpByteRange` operator-precedence bug). Unsatisfiable ranges return HTTP 416 explicitly
- Search indexer loads `storage_driver` and skips external URL documents as skipped instead of repeatedly marking them failed
- Folder-batch `scan_cursor` ensured by installer `script.php`; `#__schemas` aligned with the component manifest (non-idempotent `1.1.4.sql` ALTER corrected in 1.1.5)

### Added

- Optional HTTP cache policy for local open/download (`download_http_cache`: off / revalidate / public) with ETag, Last-Modified, and HTTP 304. Default remains private no-store. Public mode applies only to guest-accessible documents

### Changed

- Google Drive access tokens are cached until near expiry (`expires_in` minus 60 seconds), independently of site System Cache, and stored encrypted via TokenVault (not plaintext in `cache/`)
- Software template GitHub release download counts are cached for three hours independently of site System Cache (soft-fail unchanged when GitHub is unreachable)
- Frontend listing queries no longer select the full `description` column (cards use `short_description`)
- Folder scan loads the category folder map once per batch and only loads linked paths under the configured storage root
- Phrase search avoids full-index `GROUP_CONCAT` (FULLTEXT boolean phrase + per-chunk LIKE fallback)
- Content search ID loading detects the safety ceiling and shows a notice instead of failing silently
- Text indexer chunks overlap so phrases that cross a chunk boundary can match after reindex
- Frontend listing search keeps an active session across captcha redirects and applies login/length rules on listing GET
- Search user-state moved out of ListModel `.filter` keys so category `populateState()` cannot wipe the active term
- Captcha search forms block Enter/submit until anti-spam is ready (disabled submit button alone is not enough)
- Destructive / state-changing administrator actions require POST + CSRF token (indexer, backup export, Drive connect/disconnect, folder-batch run/delete, picker token)
- Admin POST action buttons no longer nest a form inside Settings/edit forms (restores Save / Save & Close / Cancel toolbar)
- Settings/folder-batch forms keep `task` at the top of `adminForm` and use a safe submitbutton so toolbar actions cannot lose the task field
- Google Drive OAuth reconnect no longer fails after Connect: single-use state nonce is stored under site `tmp/` (shared by administrator and site callback) instead of per-application Joomla cache
- Folder-batch import resumes from a persisted `scan_cursor` checkpoint so each batch does not re-walk already-handled files from the start of the folder tree
- Category search/clear fetch a fresh CSRF token from `search.security` before POST so Cloudflare-cached listing HTML does not reuse a stale form token
- Search security bootstrap uses POST (not GET) so Cloudflare Cache Everything cannot cache the JSON token/challenge or strip the session `Set-Cookie`
- Frontend search script asset renamed to `site-search-cfpost.js` so Cloudflare cannot keep serving a stale cached `site-search.js` after update
- Captcha search results require the URL `filter_search` and `filter_search_mode` to match the captcha-approved session; mismatch clears the session search and shows the clean listing (avoids per-visitor HTML under one cacheable URL)
- Without captcha, a clean category URL no longer resurrects a previous search from user-state (keeps the base listing cache key free of result HTML)
- Settings → Search shows a clear Cloudflare Cache Everything warning: Bypass cache for query string `filter_search`, after broad cache rules
- Search redirects always include `filter_search` in the URL so result pages do not share the clean category cache key; clean `/docs` drops leftover captcha search session state
- Search index replace is transactional; failed rows stay failed until rebuild/pending
- Local streaming stops on client disconnect; download counter ignores Range continuation chunks
- Google Drive streaming stops pulling from Google when the client disconnects (`connection_aborted` in the cURL write callback; Docs/Sheets export uses the same path)
- Google Drive downloads use connect timeout (30s) and low-speed abort (below 1 KiB/s for 60s) so stalled upstream peers cannot hold PHP-FPM workers indefinitely
- Category document count reuses pagination total

### Security

- URL remote size probe blocks private/reserved hosts
- Google Drive Connect requires CSRF token; OAuth state nonces are single-use
- Media filesystem adapter hard-denies dangerous extensions (shared denylist)
- TokenVault stores credentials with authenticated encryption (`sodium_crypto_secretbox`, `v2:` prefix); legacy AES-CBC blobs still decrypt and are rewritten to v2 on use

### Notes

- Requires Joomla 6.0+ and PHP 8.3.0+
- Install and update only through `pkg_devartdocuments`

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
