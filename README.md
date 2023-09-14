# TESD Ecollect Customizations

TESD Specific Ecollect customizations. Sharing for informational purposes. Currently sparsely documented and not fit for public consumption.

## Features

* Provide an auto-complete data set which includes school-wide list of students that is available to all teachers
* Expand the default "Response History" dropdown to include submitted values labeled with the custom CSS class "response-label"
* Disable Form Builder on the PS Teacher portal.

## Installation

Clone this repository, then run `pnpm install` to install dependencies.

PowerSchool plugin source code directory is `src/powerschool`. The plugin is built with `pnpm run build`. The built plugin is in `plugin_archive/{PLUGIN NAME}-{VERSION}.zip`. Two plugins are built, one for the main plugin and one for database schema definitions.
