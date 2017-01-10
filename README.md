# gulp-rev-dist-clean

`gulp-rev-dist-clean` is a gulp plugin that will allow you to easily clean the temporary and legacy files created by the [gulp-rev](https://github.com/sindresorhus/gulp-rev) plugin.

## How It Works

The plugin parse the `rev-manifest.json` file created by [gulp-rev](https://github.com/sindresorhus/gulp-rev) and compare it to your directory tree, deleting all files that shouldn't be there.

For instance, according to the following `rev-manifest.json` file:
```
{
  "css/libs.css": "css/libs-beaeb26c53.css",
  "css/main.css": "css/main-3b7de4f4f1.css",
  "js/libs.js": "js/libs-a90857797b.js",
  "js/main.js": "js/main-beaeb26c53.js"
}
```

and the following directory tree:

```
assets
|_______ css
         |_______ libs.css
         |_______ libs-beaeb26c53.css
         |_______ main.css
         |_______ main-3b7de4f4f1.css
         |_______ old-file.css
         |_______ old-file-55900dd045.css
|_______ js
         |_______ libs.js
         |_______ libs-a90857797b.js
         |_______ main.js
         |_______ main-beaeb26c53.js
```
the plugin will always delete:
- `assets/css/old-file.css`
- `assets/css/old-file-55900dd045.css`

and can optionnaly delete either the revised files (via the `keepRenamedFiles` option):
- `assets/css/libs-beaeb26c53.css`
- `assets/css/main-3b7de4f4f1.css`
- `assets/js/libs-a90857797b.js`
- `assets/js/main-beaeb26c53.js`

and/or the original files (via the `keepOriginalFiles` option):
- `assets/css/libs.css`
- `assets/css/main.css`
- `assets/js/libs.js`
- `assets/js/main.js`

## Prerequisites

You already have a task creating a manifest file via the [gulp-rev](https://github.com/sindresorhus/gulp-rev) plugin:
```
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
```
npm install --save-dev gulp-rev-dist-clean
```

## Usage

Add the plugin to the imported node modules and create a new task as following.
The plugin will clean all the matching directories according to the specified manifest file. 
```
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
- the plugin doesn't need to access the content of your files, hence the `read: false` optimization in `gulp.src()`
- the plugin only delete files (sub-directories can be emptied but wont be deleted)

## Options

Options can be passed as the second parameter:
```
gulp.src(['build/assets/**/*'], {read: false})
    .pipe(revDistClean('build/assets/rev-manifest.json', {keepRenamedFiles: false}))
```
### keepOriginalFiles
Type: `boolean`
Default: `true`

[yes/no] Keep the original dist files [yes/no] (cf. [How It Works](#how-it-works) for an exemple)


### keepRenamedFiles
Type: `boolean`
Default: `true`

[yes/no] Keep the original files generated by [gulp-rev](https://github.com/sindresorhus/gulp-rev) (cf. [How It Works](#how-it-works) for an exemple)

## License

This project is licensed under the MIT License.
