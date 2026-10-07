# DevArt Documents for Joomla

Professional document management package for Joomla 6, designed for municipalities, organizations, publishers, agencies, business websites, and high-traffic production environments.

![Joomla](https://img.shields.io/badge/Joomla-6.x-blue)
![PHP](https://img.shields.io/badge/PHP-8.3%2B-green)
![Release](https://img.shields.io/badge/Version-1.1.4-orange)
![License](https://img.shields.io/badge/License-GPLv3-red)

---

## Overview

DevArt Documents is a modern Joomla 6 document library built for managing, publishing, searching, and securely delivering files on production websites.

The extension is designed for environments where performance, ACL control, storage safety, administrator usability, and predictable updates are essential.

DevArt Documents supports:

- Local protected document storage
- Google Drive integration per category
- Joomla categories and own DevArt Documents tags
- Frontend document listings and search
- Folder scan profiles for batch imports
- Metadata backup and restore
- Media Manager filesystem adapter (document picker isolated from Local images)
- Shared DevArt document card rendering

The package includes a component, frontend module, content plugin, editor button, system integration plugin, filesystem plugin, and scheduled task plugin.

---

## Version 1.1.5

DevArt Documents 1.1.5 is an installer hotfix after 1.1.4 for Joomla 6 / PHP 8.3+. It fixes the `JInstaller Error SQL` / Duplicate column `scan_cursor` failure when updating from 1.1.3 (and when retrying after a failed 1.1.4 attempt). No changes to storage paths, ACL, routing defaults, or document data. Schema remains additive and downgrade-friendly.

### Version 1.1.5 Highlights

- Update/install from 1.1.3 no longer fails with Duplicate column `scan_cursor`
- `1.1.4.sql` is a schema marker only; `scan_cursor` is ensured idempotently in `script.php`
- Sites that failed on 1.1.4 can update directly to 1.1.5
- Includes all 1.1.4 fixes (CSRF, Drive OAuth, folder-batch checkpoint, Cloudflare-safe search, Range/416, caches, streaming harden)

See `CHANGELOG.md` for the full public changelog.

---

## Version 1.1.4

DevArt Documents 1.1.4 is a correctness, security, and performance patch after 1.1.3 for Joomla 6 / PHP 8.3+. It hardens administrator CSRF, Google Drive OAuth reconnect, folder-batch import checkpoints, Cloudflare-safe frontend search, and streaming/download behaviour. No changes to storage paths, ACL, or routing defaults. Folder-batch import adds an optional `scan_cursor` checkpoint column (empty by default). **If you hit Error SQL on update to 1.1.4, install 1.1.5 instead.**

### Version 1.1.4 Highlights

- Invalid `Range` headers rejected correctly; unsatisfiable ranges return HTTP 416
- Search indexer skips external URL documents instead of marking them failed
- Google Drive access tokens cached until near expiry (independent of System Cache; TokenVault `v2:` authenticated encryption)
- GitHub release download counts cached for three hours on Software cards
- Destructive admin actions require POST + CSRF; Settings toolbar nesting fixed; Drive OAuth reconnect via shared `tmp/` nonce
- Folder-batch import resumes from a persisted `scan_cursor` checkpoint
- Cloudflare-safe category search: POST `search.security` bootstrap, `site-search-cfpost.js`, URL/session agreement for captcha results, Settings warning for Cache Rule bypass on `filter_search`
- Optional local download HTTP cache policy (off / revalidate / public; default remains private no-store)
- Lighter listing queries, safer search/indexing, SSRF/OAuth harden, stream abort/timeouts
- No changes to storage paths, ACL, or routing defaults

See `CHANGELOG.md` for the full public changelog.

---

## Version 1.1.2

DevArt Documents 1.1.2 is a compatibility patch after 1.1.1 for Joomla 6 / PHP 8.3+. It prepares the codebase for Joomla 7 and PHP 8.5 without changing storage paths, ACL, or routing defaults.

### Version 1.1.2 Highlights

- Joomla 7 readiness: removed APIs scheduled for removal in 7.0 (`getConfig`, `getCache`, application `input` property, `Categories::getInstance`, `Table::getDbo`, view `get()`, Document script/style helpers)
- Google Drive / OAuth use the framework HTTP client (PSR-7); storage protection probe reports real HTTP status
- Frontend and administrator assets via WebAssetManager (`SiteAssets`, form validate / keepalive / multiselect)
- PHP 8.5 readiness: removed deprecated `curl_close` / `finfo_close` / `$http_response_header`
- Database queries use `createQuery()` / `setLimit()`; search session keys use `Session::remove()`

See `CHANGELOG.md` for the full public changelog.

---

## Version 1.1.1

DevArt Documents 1.1.1 was a production patch after 1.1.0 for reported fixes and small safe additions.

### Version 1.1.1 Highlights

- Own DevArt Documents tags (admin Tag Manager, frontend tag pages/filters)
- Module **Single document** scope with searchable DocumentSelect picker and detail layout
- Category override for embedded PDF preview
- ZIP/package Media upload types restored; Media picker no longer sticks on Documents storage
- Routing Off / no Documents menu detail links; search script and results-count fixes
- WebAsset path corrections for DocumentSelect and category search

See `CHANGELOG.md` for the full public changelog.

---

## Version 1.1.0

DevArt Documents 1.1.0 is the public update after 1.0.1. It consolidates all certified development work since that release into one installable package for Joomla 6.

### Version 1.1.0 Highlights

- Software and PDF display templates (GitHub links, hash, embedded PDF preview)
- Theme and display overrides on document, category, and menu with clear resolution chains
- Per-extension Open overrides in Tools → Settings
- External URL storage driver with ACL-gated redirect
- ACL-gated PDF embed shortcode and **DevArt Embed PDF** editor button
- Document Ordering for Manual ordering on menus and modules
- Google Drive security hardening and content shortcode language loading
- Release housekeeping: `LICENSE.txt`, GPLv3+ PHP headers, manifest descriptions

See `CHANGELOG.md` for the full public changelog.

---

## Version 1.0.1

DevArt Documents 1.0.1 was the first public release for Joomla 6, with a critical clean-site installation fix.

### Version 1.0.1 Highlights

- Fixed clean-site package installation failures caused by incomplete schema creation before install SQL
- Fixed installer postflight errors when component classes were not yet booted
- Fixed missing `idx_folder_batch_id` detection on fresh installations
- Installer preflight on fresh install now defers documents table creation to manifest install SQL
- Installer postflight loads storage and menu helpers without requiring a booted component
- Validated on clean Joomla 6 site install and on existing site update
- No breaking changes to existing documents, categories, or storage paths

---

## Package Contents

The installable package includes:

- `com_devartdocuments` — Document library component (administrator and site)
- `mod_devartdocuments` — Frontend document listing module
- `plg_content_devartdocuments` — Article document card and PDF embed shortcodes
- `plg_editors-xtd_devartdocuments` — Editor buttons for document card and PDF embed insertion
- `plg_system_devartdocuments` — System integration, category fields, and administrator branding
- `plg_filesystem_devartdocuments` — Media Manager filesystem adapter
- `plg_task_devartdocuments` — Scheduled search indexing and folder scan imports

Always install or update using the full `pkg_devartdocuments` package.

Do not install the standalone component ZIP on a clean website.

---

## Document Library

The component provides a full document management workflow:

- Document records with title, alias, descriptions, and image
- Joomla category integration through `com_categories`
- Joomla tag integration through `com_tags` for `com_devartdocuments.document`
- Publish up and publish down windows
- Manual ordering within categories
- Download counters
- Dashboard statistics and storage security status

Documents can be opened or downloaded according to per-document and inherited category permissions.

---

## Access Control

DevArt Documents integrates with Joomla ACL.

Supported access models include:

- Access levels
- User groups
- Inheritance from category
- Per-document open permission
- Per-document download permission
- SQL-level ACL filtering on frontend listings

Frontend listings use bounded queries with ACL-aware filters for large production databases.

---

## Storage

### Local Storage

Local files are stored in a protected storage root managed by `StorageProtectionHelper`.

Local storage features include:

- Configurable relative storage folder
- Directory hardening with deny rules
- Dashboard storage security status
- MIME validation through `DocumentMimeMap`
- HTTP 206 Partial Content support for local open and download through `HttpByteRange`

### Google Drive

Google Drive can be configured per category.

Google Drive features include:

- Per-category folder ID configuration
- OAuth token storage with encrypted token vault
- Streaming and chunked upload paths
- Streaming serve paths for frontend open and download
- Streaming search indexing paths
- Google Drive picker constrained to the configured category folder

Google Drive open and download does not support HTTP Range. Range requests apply to local files only.

---

## Frontend Presentation

DevArt Documents uses a shared document card renderer across:

- Category menu item views
- Frontend module output
- Content plugin article rendering

Presentation features include:

- DevArt document card UI
- Display themes: red, orange, blue, green, yellow, gray, and dark
- Software and PDF display templates
- ACL-gated PDF embed in document detail and article shortcodes
- `DocumentContentScope` for all documents, selected categories, or tags
- `DisplaySettingsResolver` for consistent listing behavior
- Category menu scope stored in menu parameters
- Clean canonical category URLs without scope query variables in the public link

Routing and display settings are configured in **Tools → Settings**, not in generic Joomla component options.

---

## Search

Full-text document search runs on the category menu item page.

Search features include:

- Local search index tables inside the component database
- Optional external MariaDB search index backend
- Search index administration under **Tools → Search index**
- Scheduled indexing through `plg_task_devartdocuments`
- CAPTCHA and rate limits on public search endpoints
- Pending reindex marking when documents are saved

Image-only PDFs may be skipped by the indexer until OCR support exists.

---

## Folder Scan Profiles

Folder scan profiles allow batch imports from disk.

Features include:

- Saved scan profiles under **Tools → Folder scan profiles**
- Recursive directory scanning
- Title and alias pattern options
- Batch size controls
- Access inheritance from profiles
- Scheduled imports through the task plugin

Global folder-scan fields were removed from Settings in favor of profile-based workflows.

---

## Metadata Backup

Metadata backup is available under **Tools → Metadata backup**.

Administrators can:

- Export document metadata to JSON
- Import metadata from JSON backups
- Transfer configuration between environments without moving raw files

---

## Media Manager Integration

The filesystem plugin exposes the protected document storage area to Joomla Media Manager.

MIME handling is aligned with `DocumentMimeMap` for upload safety and consistency with frontend serving rules.

---

## Administrator Tools

The component administrator area includes:

- Dashboard
- Documents
- Categories (Joomla `com_categories`)
- Tags (Joomla `com_tags`)
- Tools:
  - Settings
  - Search index
  - Folder scan profiles
  - Metadata backup

Captcha keys for search are configured in Joomla **Options → Security integrations**.

---

## Frontend Performance

DevArt Documents is designed for large Joomla websites and high-traffic environments.

Performance characteristics include:

- Bounded listing queries through `DocumentListingQuery`
- SQL ACL filters instead of post-query permission checks
- Listing indexes: `idx_downloads`, `idx_catid_state_ordering`, `idx_folder_batch_state`
- Streaming Google Drive paths for serve and indexing
- Lightweight native JavaScript
- No jQuery dependency
- Namespaced CSS
- Cloudflare-friendly rendering
- Joomla module caching support on the Advanced tab

### Cloudflare Cache Everything (required ops)

If the site uses Cloudflare **Cache Everything** (or similar edge HTML caching) with an Edge TTL that ignores origin `Cache-Control`, add Cache Rules so dynamic Documents URLs are never stored at the edge:

1. Broad frontend cache rule(s) first.
2. **Bypass last** (last matching Cache Rule wins — not legacy Page Rules):
   - `*/administrator*` (and custom admin login URLs)
   - query string contains `filter_search=` (category search result pages)

Category search uses a POST security bootstrap and session/URL agreement so captcha results stay visitor-safe; the `filter_search` bypass is still required when Edge TTL ignores `no-store`. Details: `qa/checklist.md` (Cloudflare Cache Rules order).

---

## Joomla-Native Architecture

DevArt Documents follows modern Joomla development patterns, including:

- Joomla 6 MVC architecture
- Namespaced PHP classes with strict types
- Joomla service provider architecture
- Joomla Web Asset Manager
- Joomla Form API
- Joomla ACL
- Joomla database APIs
- Joomla administrator Atum layouts
- CSRF protection
- Proper input filtering
- Escaped output

No Joomla 3, 4, or 5 compatibility layer is included.

---

## Security

Security measures include:

- Controller to service to storage flow
- No public exposure of raw filesystem paths
- ACL enforcement for administrator and frontend access
- CSRF protection for administrator actions
- MIME validation for upload, serve, and filesystem adapter operations
- Protected local storage root with deny rules
- Encrypted OAuth token storage for Google Drive
- Search CAPTCHA and rate limiting controls
- Installer checksum support through update metadata SHA-256

Nginx and LiteSpeed require explicit server deny rules for the storage path. `.htaccess` alone is not sufficient on those servers.

---

## Requirements

- Joomla 6.0 or newer
- PHP 8.3 or newer
- MySQL or MariaDB supported by Joomla 6
- A modern browser for administrator and frontend interfaces

Optional:

- Google account and API credentials for Google Drive storage
- Separate MariaDB database for external search index backend
- CAPTCHA provider configuration for public search protection

---

## Installation

1. Download the latest package:

   `pkg_devartdocuments_v1.1.0.zip`

2. Open the Joomla administrator.

3. Go to:

   `System → Install → Extensions`

4. Upload and install the **full package**.

5. Open:

   `Components → DevArt Documents`

6. Configure storage and routing in **Tools → Settings**.

7. Create categories and documents.

8. Publish a category menu item or frontend module as required.

The package supports installation and updates through the standard Joomla Extensions installer.

---

## Updating

DevArt Documents uses the standard Joomla update system.

Update server:

`https://raw.githubusercontent.com/devartgr/joomla-devart-documents/main/update.xml`

Before updating a production website:

- Create a complete backup
- Test the update on a staging environment
- Verify the component administrator
- Verify document open and download
- Verify search and folder scan workflows if used
- Run **System → Maintenance → Database** and confirm no problems
- Clear frontend and CDN caches when necessary

Version 1.1.5 is a safe update from 1.1.3, from a failed 1.1.4 attempt, and from earlier 1.1.x / 1.0.1 releases. Always install or update with the full `pkg_devartdocuments` package ZIP. Prefer 1.1.5 over 1.1.4.

If the site uses Cloudflare Cache Everything, add a Cache Rule that bypasses cache when the URI query string contains `filter_search`, and place it after any broad cache rule (see README section Cloudflare Cache Everything).

---

## Download

Latest release:

`pkg_devartdocuments_v1.1.5.zip`

GitHub releases:

https://github.com/devartgr/joomla-devart-documents/releases

Direct download:

https://github.com/devartgr/joomla-devart-documents/releases/download/v1.1.5/pkg_devartdocuments_v1.1.5.zip

SHA-256:

`242324f2ac6c4274ba415ae516a4b32aca7c149de7add259e8c92d71b2906d92`

---

## Support and Documentation

Project repository:

https://github.com/devartgr/joomla-devart-documents

Website:

https://devart.gr

Before reporting an issue, include:

- Joomla version
- PHP version
- Database type and version
- DevArt Documents version
- Storage driver in use (local or Google Drive)
- Relevant error message
- Steps required to reproduce the issue

Do not include passwords, private keys, access tokens, or other sensitive information.

---

## License

DevArt Documents is released under the GNU General Public License version 3 or later.

See `LICENSE.txt` for complete licensing information.

---

## Author

**Kostas Stathopoulos — DevArt**

Website: https://devart.gr
