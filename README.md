# WYSIWYG
[![License](https://img.shields.io/badge/license-MIT-10b981.svg)](https://github.com/enso-ui/wysiwyg/blob/master/LICENSE)
[![Stable](https://img.shields.io/badge/stable-3.2.3-2563eb.svg)](https://www.npmjs.com/package/@enso-ui/wysiwyg)
[![Downloads](https://img.shields.io/npm/dm/@enso-ui/wysiwyg.svg)](https://www.npmjs.com/package/@enso-ui/wysiwyg)
[![Vue](https://img.shields.io/badge/vue-3.x-42b883.svg)](https://vuejs.org/)
[![JavaScript](https://img.shields.io/badge/javascript-ES2020-f7df1e.svg)](https://developer.mozilla.org/docs/Web/JavaScript)
[![SCSS](https://img.shields.io/badge/scss-supported-c6538c.svg)](https://sass-lang.com/)
[![npm](https://img.shields.io/badge/npm-package-cb3837.svg)](https://www.npmjs.com/package/@enso-ui/wysiwyg)
[![Issues](https://img.shields.io/github/issues/enso-ui/wysiwyg.svg)](https://github.com/enso-ui/wysiwyg/issues)
[![Merge Requests](https://img.shields.io/github/issues-pr/enso-ui/wysiwyg.svg)](https://github.com/enso-ui/wysiwyg/pulls)
## Description
WYSIWYG field for Enso UI, with TinyMCE as the default editor and optional Trix support.
## Installation
Install the package:

```bash
yarn add @enso-ui/wysiwyg
```
## Features
- exports `Wysiwyg` as its public surface
- supports `tinymce` and `trix` editor engines
- keeps TinyMCE as the default editor for backward compatibility
- keeps the Bulma presentation layer separate from the renderless/stateful layer where applicable
## Usage
```vue
<script setup>
import Wysiwyg from '@enso-ui/wysiwyg/bulma';
</script>

<Wysiwyg v-model="content"
    :has-error="false"/>

<Wysiwyg v-model="content"
    editor="trix"
    :has-error="false"/>
```
## API
### `Wysiwyg`

Public export available from `src/bulma/Wysiwyg.vue`.

Props:
- `hasError`
- `editor`
- `menubar`
- `plugins`
- `toolbar`

`editor` defaults to `tinymce`. Set it to `trix` to use the Trix editor.

### Trix

Trix uses the same `v-model` contract as TinyMCE:

```vue
<Wysiwyg v-model="content"
    editor="trix"
    :has-error="false"/>
```

Form renderers may pass this through backend metadata as `meta.editor: 'trix'`.

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

## Depends On
- No additional Enso UI package dependencies.
- [`trix`](https://github.com/basecamp/trix)
## Contributions
are welcome. Pull requests are great, but issues are good too.
Thank you to all the people who already contributed to Enso!
## License
[MIT](https://github.com/enso-ui/wysiwyg/blob/master/LICENSE)
