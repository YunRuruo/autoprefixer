# Autoprefixer Core [![Build Status](https://travis-ci.org/postcss/autoprefixer-core.png)](https://travis-ci.org/postcss/autoprefixer-core)

<img align="right" width="94" height="71" src="http://ai.github.io/autoprefixer/logo.svg" title="Autoprefixer logo by Anton Lovchikov">

Autoprefixer is a tool to parse CSS and add vendor prefixes to CSS rules
using values from [Can I Use](http://caniuse.com/).

This is core package to build Autoprefixer plugin for some environment
(like [grunt-autoprefixer]). For end-user documentation, features
and plugins list visit [main Autoprefixer] project.

Sponsored by [Evil Martians]. Based on [PostCSS] framework.

[grunt-autoprefixer]: https://github.com/nDmitry/grunt-autoprefixer)
[main Autoprefixer]:  https://github.com/ai/autoprefixer
[Evil Martians]:      http://evilmartians.com/
[PostCSS]:            https://github.com/postcss/postcss

## Quick Example

Write your CSS rules without vendor prefixes (in fact, forget about them
entirely):

```css
:fullscreen a {
    transition: transform 1s
}
```

Process your CSS by Autoprefixer:

```js
var autoprefixer = require('autoprefixer-core');
var prefixed = autoprefixer.process(css).css;
```

It will use the data based on current browser popularity and property support
to apply prefixes for you:

```css
:-webkit-full-screen a {
    -webkit-transition: -webkit-transform 1s;
            transition: transform 1s;
}
:-moz-full-screen a {
    transition: transform 1s;
}
:-ms-fullscreen a {
    transition: transform 1s;
}
:fullscreen a {
    -webkit-transition: -webkit-transform 1s;
            transition: transform 1s;
}
```

## Usage

To process your CSS you need to make 2 steps:

1. Build processor for your options and browsers supported in your project.
2. Process CSS throw this processor.

Function `autoprefixer(browser1, browser2, …, options)` returns
new processor object:

```js
var processor = autoprefixer('> 1%', 'Explorer 7', { cascade: false });
```

You can directly specify browser version (like `iOS 7`) or use selections
(like `last 2 version` or `> 5%`). [Full browsers documentation] is available
on main Autoprefixer page.

By default, Autoprefixer uses
`'> 1%', 'last 2 versions', 'Firefox ESR', 'Opera 12.1'`. You can get current
default list from `autoprefixer.default` property.

There is only one option right now: `cascade: false` will disable
[Visual Cascade].

Processor object had:

* `.process(css, opts)` method, which will add prefixes to `css`.
* `.info()` method, which returns debug information: which browsers are selected
  and which properties will be prefixed
* `.postcss` property returns [PostCSS] processor to use in chain
  with [other PostCSS processors].

You can use processor object to process several CSS files. It will be faster,
that create processor each time.

There are `autoprefixer.process()`, `autoprefixer.info()`
and `autoprefixer.postcss` shortcuts, which use default browsers and options.

[Full browsers documentation]: https://github.com/ai/autoprefixer#browsers
[other PostCSS processors]:    https://github.com/postcss/postcss#built-with-postcss
[Visual Cascade]:              https://github.com/ai/autoprefixer#visual-cascade
[PostCSS]:                     https://github.com/postcss/postcss

## PostCSS Chain

You parse CSS only once and then process it through array of PostCSS processors.

For example, you can use [gulp-postcss]:

```js
var postcss    = require('gulp-postcss');
var sourcemaps = require('gulp-sourcemaps');

gulp.task('css', function () {
    var processors = [
        require('autoprefixer')('last 1 version').postcss,
        require('css-mqpacker').processor,
        require('csswring').postcss
     ];
     return gulp.src('./src/style.css')
        .pipe(sourcemaps.init())
        .pipe(postcss(processors))
        .pipe(sourcemaps.write('.'))
        .pipe(gulp.dest('./dest'));
});
```

[gulp-postcss]: https://github.com/w0rm/gulp-postcss
