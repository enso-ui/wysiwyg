# Changelog

## 3.2.4

### Fixed

- load TinyMCE skin styles from the package dependency
- remove plugins that require runtime assets outside the bundled module graph

## 3.2.3

### Fixed

- load TinyMCE from the package dependency instead of relying on the external loader
- added the TinyMCE code plugin to expose source editing

## 3.2.0

### Added

- added optional Trix editor support through the `editor` prop
- kept TinyMCE as the default editor for backward compatibility
- added Trix styling aligned with the Bulma theme tokens used by the existing WYSIWYG field
