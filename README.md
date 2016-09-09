# gulp-foal

# Install 安装

```
npm install gulp-foal --save-dev
```

# Basic Usage 基本用法

Use gulp-foal like this:

```javascript
'use strict';

var gulp = require('gulp');
var clean = require('gulp-clean');
var foal = require('gulp-foal');

//use "foal.task(...)" to define a foal-task.
//使用 foal.task(...) 来定义一个 foal 任务
foal.task('clean_some', function(cleanPath) {
  //"return" shall not be missed for running foal-task orderly.
  //为了顺序执行 foal 任务，请别省略 return 语句
  return gulp.src(cleanPath)
    .pipe(clean());
});

gulp.task('default', function(cb) {
  //use "foal.run(...)" to run your foal-task with param.
  //使用 foal.run(...) 来执行带参数（当然也可以不带）的 foal 任务
  foal.run(clean_some('test_path'), cb);
});

```

## foal.run()

It ran just like [gulp-sequence](https://www.npmjs.com/package/gulp-sequence) which means you can use it like this:

它跑起来和 [gulp-sequence](https://www.npmjs.com/package/gulp-sequence) 类似，你可以像下面的例子那样使用它：

```javascript
foal.run(task1('param'), [task2('param'), task3()], task4('param'), cb);
```

## foal.task()

**Foal-task is bind to global by default.** If you don't want it to, use like this:

**foal 任务默认被绑定在全局环境下，** 如果你不希望这样，如下

```javascript
foal.task('test', {
  bindToFoal: true
}, function(param) {/*...*/});

foal.run(foal.test(param));
```

# Task Combination 合并的任务

Combine your foal-task and run them like this: 

如下所示，合并执行你的任务：

```javascript
gulp.task('default', ['all_page']);

gulp.task('all_page', () => {
  return gulp.src(`${PATH.DEVELOP}/page/*/`)
    .pipe(through.obj(function(file, enc, cb) {
      this.push(file);
      const pageName = file.relative;
      const pagePath = `${PATH.DEVELOP}/page/${pageName}`;
      foal.run(page(pageName, pagePath), cb);
    }));
});

foal.task('page', (pageName, pagePath) => {
  return foal.run([js(pageName, pagePath), css(pageName, pagePath)]);
});

foal.task('page_jsx', (pagePath) => {
  return gulp.src(`${pagePath}/**/*.jsx`)
    .pipe(react())
    .pipe(gulp.dest(`${pagePath}/.transform`))
});

foal.task('page_js', (pageName, pagePath) => {
  return gulp.src([`${PATH.DEVELOP}/common/.transform/**/*.js`, `${pagePath}/**/*.js`, `${pagePath}/.transform/**/*.js`])
    .pipe(concat(`${pageName}.js`))
    .pipe(gulp.dest(`${pagePath}/.build`))
})

foal.task('js', (pageName, pagePath) => {
  return foal.run(page_jsx(pagePath), page_js(pageName, pagePath));
});

foal.task('css', (pageName, pagePath) => {
  return gulp.src(`${pagePath}/*.scss`)
    .pipe(sass().on('error', sass.logError))
    .pipe(rename(`${pageName}.css`))
    .pipe(gulp.dest(`${pagePath}/.build`));
});

```

# The MIT License (MIT)
Copyright (c) 2016 CJY fanluan058@gmail.com

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.