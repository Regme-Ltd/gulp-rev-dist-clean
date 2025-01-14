[![npm version](https://badgen.net/npm/v/gulp-rev-dist-clean)](https://www.npmjs.com/package/gulp-rev-dist-clean)
&nbsp;
[![travisci](https://badgen.net/travis/alexandre-abrioux/gulp-rev-dist-clean)](https://travis-ci.com/alexandre-abrioux/gulp-rev-dist-clean)
&nbsp;
[![Coverage Status](https://coveralls.io/repos/github/alexandre-abrioux/gulp-rev-dist-clean/badge.svg?branch=master)](https://coveralls.io/github/alexandre-abrioux/gulp-rev-dist-clean?branch=master)
&nbsp;
![npm downloads](https://badgen.net/npm/dt/gulp-rev-dist-clean)
&nbsp;
[![xo](https://badgen.net/xo/status/gulp-rev-dist-clean)](https://github.com/xojs/xo)
&nbsp;
[![license](https://badgen.net/github/license/alexandre-abrioux/gulp-rev-dist-clean)](https://github.com/alexandre-abrioux/gulp-rev-dist-clean/blob/master/LICENSE)

# gulp-rev-dist-clean

`gulp-rev-dist-clean` is a gulp package to ease clean-up of temporary and legacy files created by the [gulp-rev](https://github.com/sindresorhus/gulp-rev) plugin.

[![NPM](https://nodei.co/npm/gulp-rev-dist-clean.png)](https://www.npmjs.com/package/gulp-rev-dist-clean)

## How It Works

The plugin parses the `rev-manifest.json` file created by [gulp-rev](https://github.com/sindresorhus/gulp-rev) and compares it to your directory tree, deleting tempory files that you do not wish to keep.

For instance, according to the following `rev-manifest.json` file:
```json
{
  "css/libs.css": "css/libs-beaeb26c53.css",
  "css/main.css": "css/main-3b7de4f4f1.css",
  "js/libs.js": "js/libs-a90857797b.js",
  "js/main.js": "js/main-beaeb26c53.js"
}
```

and the following directory tree:

```
build/assets
├─── css
│    ├─── libs.css
│    ├─── libs-beaeb26c53.css
│    ├─── main.css
│    ├─── main-3b7de4f4f1.css
│    ├─── old-file.css
│    └─── old-file-55900dd045.css
├─── js
│    ├─── libs.js
│    ├─── libs-a90857797b.js
│    ├─── main.js
│    └─── main-beaeb26c53.js
└─── rev-manifest.json
```
the plugin will always delete the following files, because they are not listed in the manifest:
- `build/assets/css/old-file.css`
- `build/assets/css/old-file-55900dd045.css`

It can optionally remove either the revised files (via the `keepRenamedFiles` option):
- `build/assets/css/libs-beaeb26c53.css`
- `build/assets/css/main-3b7de4f4f1.css`
- `build/assets/js/libs-a90857797b.js`
- `build/assets/js/main-beaeb26c53.js`

and/or the original files (via the `keepOriginalFiles` option):
- `build/assets/css/libs.css`
- `build/assets/css/main.css`
- `build/assets/js/libs.js`
- `build/assets/js/main.js`

and/or the manifest file itself (via the `keepManifestFile` option):
- `build/assets/rev-manifest.json`

## Prerequisites

You already have a task creating a manifest file via the [gulp-rev](https://github.com/sindresorhus/gulp-rev) plugin:
```js
const gulp = require('gulp');
const rev = require('gulp-rev');

gulp.task('default', () =>
    gulp.src(['assets/css/*.css', 'assets/js/*.js'], {base: 'assets'})
        .pipe(gulp.dest('build/assets'))
        .pipe(rev())
        .pipe(gulp.dest('build/assets'))
        .pipe(rev.manifest())
        .pipe(gulp.dest('build/assets'))
);
```

## Installation

First, install the plugin via npm:
```bash
npm install --save-dev gulp-rev-dist-clean
```

## Usage

Add the plugin to the imported node modules and create a new task as following.
The plugin will clean all the matching directories according to the specified manifest file. 
```js
const gulp = require('gulp');
const rev = require('gulp-rev');
const revDistClean = require('gulp-rev-dist-clean');

gulp.task('default', () =>
    gulp.src(['assets/css/*.css', 'assets/js/*.js'], {base: 'assets'})
        .pipe(gulp.dest('build/assets'))
        .pipe(rev())
        .pipe(gulp.dest('build/assets'))
        .pipe(rev.manifest())
        .pipe(gulp.dest('build/assets'))
);

gulp.task('rev-dist-clean', () =>
    gulp.src(['build/assets/**/*'], {read: false})
        .pipe(revDistClean('build/assets/rev-manifest.json'))
);
```

## Notes
- the plugin doesn't need to access the content of your files, hence you can add the `read: false` optimization in `gulp.src()`
- the plugin only deletes files (sub-directories are emptied but wont be deleted)

## Options

Options can be passed as the second parameter:
```js
gulp.src(['build/assets/**/*'], {read: false})
    .pipe(revDistClean('build/assets/rev-manifest.json', {keepRenamedFiles: false}))
```
### keepOriginalFiles
Type: `boolean`

Default: `true`

Keep the original files? \[yes/no\] (cf. [How It Works](#how-it-works) for an example)


### keepRenamedFiles
Type: `boolean`

Default: `true`

Keep the revised files generated by [gulp-rev](https://github.com/sindresorhus/gulp-rev)'s `rev()` method? \[yes/no\]
(see [How It Works](#how-it-works) for an example)


### keepManifestFile
Type: `boolean`

Default: `true`

Keep the manifest file generated by [gulp-rev](https://github.com/sindresorhus/gulp-rev)'s `rev.manifest()` method? \[yes/no\]
(see [How It Works](#how-it-works) for an example)

### emitChunks
Type: `boolean`

Default: `false`

Set to `true` if you need to `pipe()` the files to another gulp plugin.

When set to false nothing will get emitted to the stream.

### forceDelete
Type: `boolean`

Default: `false`

Set to `true` if you want force delete files in your directory. This option remove error
```
Cannot delete files/folders outside the current working directory. Can be overriden with the `force` option.
```

## License

This project is licensed under the MIT License.
