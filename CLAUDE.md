# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a PowerSchool plugin that customizes the eCollection/Form Builder functionality for TESD (Tredyffrin-Easttown School District). The plugin provides enhanced auto-complete datasets, response history features, form approval workflows, and UI customizations across multiple PowerSchool portals (Admin, Teacher, Guardian).

## Build Commands

- **Build plugin**: `pnpm run build` or `npm run build`
  - Uses the `@tesd-tech/ps-package` tool to package the plugin
  - Creates two ZIP files in `plugin_archive/`:
    - `TE Tech - Ecollect Customizations-{VERSION}.zip` (main plugin)
    - `DATA-TE Tech - Ecollect Customizations-{VERSION}.zip` (schema plugin)
  - Copies files from `src/powerschool/` to `dist/` during build

- **Install dependencies**: `pnpm install` or `npm install`

- **Update dependencies**: `npx npm-check-updates` (check for updates)

## Architecture

### Two-Plugin System

This project generates **two separate PowerSchool plugins**:

1. **Main Plugin**: Contains all UI customizations, scripts, datasets, and custom pages
2. **DATA Plugin**: Contains database schema definitions (minimal in current implementation)

Both plugins share the same version number and are built simultaneously.

### Portal-Based Organization

PowerSchool plugins use a portal-based directory structure under `src/powerschool/WEB_ROOT/`:

- `wildcards/` - Global content available to all portals
- `admin/` - Administrator portal customizations (students, faculty, formbuilder subdirectories)
- `teachers/` - Teacher portal customizations
- `guardian/` - Guardian portal customizations

### File Naming Convention

PowerSchool recognizes plugin customizations by filename pattern:
```
{content-id}.{plugin-name}.{injection-point}.txt
```

Examples:
- `commonscripts.tesd_ecollect_customizations.content.header.txt` - Injected in page header
- `form.tesd_ecollect_customization.content.footer.txt` - Injected in form footer
- `teachers_nav_css.tesd_ecollect_customizations.leftnav.footer.txt` - Injected in left navigation

The plugin name is `tesd_ecollect_customizations` (or `tesd_ecollect_customization` - both variants exist).

### Customization Types

The plugin implements five types of customizations:

#### 1. Auto-Complete Datasets (JSON)
- Student discipline dropdown with school-wide student list
- Home language survey using NCES language codes
- Deployed to multiple portals for consistency

#### 2. Response History Enhancement (JavaScript)
- SQL-driven enhancement of form response history dropdowns
- Appends custom labels from elements with `response-label` CSS class
- Uses polling mechanism to wait for Angular component loading

#### 3. Content Injection via Footer Scripts (HTML/JavaScript)
- Email notifications for form follow-ups
- Form approval workflow automation
- Document preview using k12drive API
- Auto-sorting of attachments by upload date
- Uses MutationObserver for async Angular component integration

#### 4. Navigation and UI Controls
- Disables Form Builder on Teacher Portal using CSS
- Adds custom navigation items with missing form counts
- SQL-driven badge counts on navigation items

#### 5. Custom Pages
- Standalone HTML reports (e.g., `discipline_form_responses.html`)
- Teacher-filtered views with SQL CTEs

### PowerSchool Template Language

The plugin extensively uses PowerSchool's template language:

- `~[tlist_sql;...]` - Execute SQL query and format results
- `~[if#portal.~[directory]=...]` - Conditional logic based on portal
- `~[wc:dataset_name]` - Reference wildcard/dataset content
- `~(variable)` - Variable substitution

### Async UI Integration Patterns

Multiple files use patterns to integrate with PowerSchool's Angular-based UI:

- **MutationObserver**: Waits for Angular components to render before executing scripts
- **setTimeout polling**: "Hammer" pattern polls for UI elements (typically 5 second timeout)
- **jQuery event binding**: Listens for dynamic content changes

### Database Tables

Common tables referenced in SQL queries:

- `S_PA_LANGUAGE_CODE_S` - Language codes
- `U_FB_FORM*` - Form builder tables
- `students` with `U_DEF_EXT_STUDENTS` - Student data and custom fields
- `teachers`, `users` - Permission and user data

## Version Management

The version number appears in two places and must be kept in sync:
- `package.json` - `version` field
- `plugin.xml` (both root and schema) - `version` attribute

Current versioning follows PowerSchool releases (e.g., 25.2.0 for PowerSchool 25.x).

## Key Files

- `plugin.xml` - Main plugin manifest with metadata
- `schema/plugin.xml` - Database schema plugin manifest
- `src/powerschool/WEB_ROOT/` - All plugin source code organized by portal
- `package.json` - Build configuration and dependencies
- `plugin_archive/` - Generated plugin ZIP files ready for PowerSchool installation

## Development Notes

### Testing and Deployment
This plugin has no automated tests. Testing is performed by installing the built ZIP files into a PowerSchool instance and verifying functionality in each affected portal.

### JavaScript Dependencies
The plugin relies on PowerSchool's built-in libraries:
- jQuery (provided by PowerSchool)
- Angular (PowerSchool's UI framework)
- PowerSchool template engine

### SQL Query Patterns
SQL queries use PowerSchool's template language and must follow these patterns:
- Queries are embedded directly in `.txt` files using `~[tlist_sql;...]` syntax
- Results are formatted using PowerSchool's template formatting options
- Security context is handled by PowerSchool's role-based access control

### Portal Context Detection
To execute code conditionally based on portal:
```
~[if#portal.~[directory]=admin/students]
  <!-- Code for admin students portal -->
~[/if]
```

### Adding New Customizations

To add a new customization:

1. Determine target portal(s) and injection point
2. Create file with proper naming convention in `src/powerschool/WEB_ROOT/{portal}/`
3. Use PowerSchool template language for dynamic content
4. Implement async handling if modifying Angular-rendered UI
5. Run `pnpm run build` to generate updated plugin packages
6. Test by installing in PowerSchool instance
