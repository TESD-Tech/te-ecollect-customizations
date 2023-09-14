# TESD Ecollect Customizations

TESD Specific Ecollect customizations. Sharing for informational purposes. Currently sparsely commented and not fit for public consumption.

## Features

* Provide an auto-complete dataset which includes school-wide list of students that is available to all teachers
* Expand the default "Response History" dropdown to include submitted values labeled with the custom CSS class "response-label"
* Disable Form Builder on the PS Teacher portal.
* Provide an auto-complete dataset which uses NCES language code for the value and the description of the language for the label.

Note: The PS Teacher Portal Discipline Form Responses Report will not work without also having other TESD plugins installed. It is included for informational purposes only.

## Installation

Clone this repository, then run `pnpm install` to install dependencies.

PowerSchool plugin source code directory is `src/powerschool`. The plugin is built with `pnpm run build`. The built plugin is in `plugin_archive/{PLUGIN NAME}-{VERSION}.zip`. Two plugins are built, one for the main plugin and one for database schema definitions.
