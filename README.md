# DevArt Documents for Joomla

Professional document management package for Joomla 6, designed for municipalities, organizations, publishers, agencies, business websites, and high-traffic production environments.

![Joomla](https://img.shields.io/badge/Joomla-6.x-blue)
![PHP](https://img.shields.io/badge/PHP-8.3%2B-green)
![Release](https://img.shields.io/badge/Version-1.0.1-orange)
![License](https://img.shields.io/badge/License-GPLv3-red)

---

## Overview

DevArt Documents is a modern Joomla 6 document library built for managing, publishing, searching, and securely delivering files on production websites.

The extension is designed for environments where performance, ACL control, storage safety, administrator usability, and predictable updates are essential.

DevArt Documents supports:

- Local protected document storage
- Google Drive integration per category
- Joomla categories and tags
- Frontend document listings and search
- Folder scan profiles for batch imports
- Metadata backup and restore
- Media Manager filesystem adapter
- Shared DevArt document card rendering

The package includes a component, frontend module, content plugin, editor button, system integration plugin, filesystem plugin, and scheduled task plugin.

---

## Version 1.0.1

DevArt Documents 1.0.1 is the first public release for Joomla 6, with a critical clean-site installation fix.

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
- `plg_content_devartdocuments` — Article document card rendering
- `plg_editors-xtd_devartdocuments` — Editor button for document insertion
- `plg_system_devartdocuments` — System integration and administrator branding
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

   `pkg_devartdocuments_v1.0.1.zip`

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

Version 1.0.1 is a safe update from alpha development builds and from version 1.0.0 package installs.

---

## Download

Latest release:

`pkg_devartdocuments_v1.0.1.zip`

GitHub releases:

https://github.com/devartgr/joomla-devart-documents/releases

Direct download:

https://github.com/devartgr/joomla-devart-documents/releases/download/v1.0.1/pkg_devartdocuments_v1.0.1.zip

SHA-256:

`0f2580986f5cda50f09dc2b977429c42c706561ce54132f62aa957acfaa523a4`

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

See the included license file for complete licensing information.

---

## Author

**Kostas Stathopoulos — DevArt**

Website: https://devart.gr
