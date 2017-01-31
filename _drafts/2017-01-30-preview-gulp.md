---
layout: post
title: "Preview Gulp.js"
date: "2017-01-30"
---

회사 입사 후 3밀만에 발표를 마치고 다음 주제를 결정하려는데 제가 gulp를 하기로 했습니다. 관심 있던 주제이기도 하고 알아두면 참 편리 할 것 같았습니다.

# Make tool

갑작스럽고 뜬금 없지만 옛날 이야기 : 과거 리눅스에서 개발하는 c 개발자들은 혼란하고 복잡하게 나뉘어진 c파일을 일일이 컴파일해서 합치는 과정에 신물이나서 make라는 훌륭한 도구를 만들어 낸다. make라는 툴은 컴파일을 과정을 서술한 'Makefile'의 내용대로 컴파일을 자동으로 하는 아주아주 훌륭한 툴이다. 말로만보면 모르겠으니 Makefile의 내용을 봅시다.

[![Makefile 문법](http://cfile4.uf.tistory.com/image/242A274956E5920F2BC750)](http://bowbowbow.tistory.com/12)
[![Makefile 예제](http://cfile27.uf.tistory.com/image/2714AC4A56E592A6080DB5)](http://bowbowbow.tistory.com/12)
출처: http://bowbowbow.tistory.com/12 [멍멍멍]


### 작성 규칙
* 매크로를 참조 할 때는 소괄호나 중괄호 둘러싸고 앞에 ‘$’를 붙인다.
* 탭으로 시작해서는 안되고 , :,=,#,”” 등은 매크로 이름에 사용할 수 없다.
* 매크로는 반드시 치환될 위치보다 먼저 정의 되어야 한다.

원래 diary.exe라는 파일을 만들기 위해서는 위의 3가지 object file(memo.0, calendar.o, main.o)가 필요하고 그 파일들을 만들어 주기 위해 각각을 컴파일을 해야한다. 하지만 저 명세와 함깨 마법의 명령어 `make all`을 입력하면 된다.


# 혼란스러운 javascript 프로젝트

최근 웹 개발은 각각의 모듈화로 인해 파일의 겟수가 많아지고 디렉토리 경로 역시 복잡해지고 있다. 특히 프론트엔드의 경우에는 프론트엔드에서 진행되어야 할 부분까지 커버해야 하기도 할 때가 생겼기 때문이다. 이에 할 일(concatenating, minifying, validating)이 많아진 프론트엔드 프로젝트의 **자동화를 편리하게 관리**하기 위해 빌드 시스템들이 등장 하기 시작했다.


# Build system
빌드 시스템을 한마디로 정리하면 연관된 작업모음의 자동화(보통 task runner라 한다)이다.[(출처)](http://joellongie.com/gulp-build-system-fundamentals/) 
![Building system](https://i1.wp.com/joellongie.com/wp-content/uploads/2015/02/web-workflow-componets.jpg)
웹 개발자를 예로 든다면 프로젝트 파일 중 하나를 수정하면 프로젝트 결과물을 자동으로 배포하는(scss파일을 css파일로 컴파일, js파일들을 minifying하여 하나로 묶는 작업, 작업된 파일을 dist폴더로 이동 등등)작업을 자동화 시킨 것과 같다.  


# Javascript build tool

자바스크립트 빌드툴이라고 불리우는 대표적인 것들이 grunt, webpack, browserify, gulp가 존재하며 2016년 사용현황은 다음과 같다. 

[![javascript-build-tool-user-chart](/images/2017-01-30-preview-gulp/build-tool-userchart.png)](http://stateofjs.com/2016/buildtools/)

* 그래프와 같이 webpack과 gulp의 인기가 높으며, 습득하다 떠나는 경우가 많이 발생하지 않는다.
* grunt는 러닝커브가 높아 사용도중 포기하는 경우가 많으며 수요조차 많이 떨어지고 있다.


[![javascript-build-tool-user-chart](/images/2017-01-30-preview-gulp/build-tool-usage-ratio.PNG)](http://stateofjs.com/2016/buildtools/)

* webpack은 주로 react진영의 도구들 (react.js, es6(babal), redux 등)에서 사용되고 있다. 
* gulp와 grunt는 주로 bower나 Angular, Backbone, Jasmine에서 쓰이고 있다.

# gulp?

![gulp logo](https://raw.githubusercontent.com/gulpjs/artwork/master/gulp-2x.png)

github.com에 있는 gulp.js의 메인에서는 gulp를 다음과 같이 정의 하고 있다. [링크](https://github.com/gulpjs/gulp#what-is-gulp)

* 자동화 - gulp는 당신의 개발 workflow의 고통스럽고(painful) 시간낭비인(time-consuming)작업의 자동화를 도와주는 툴킷입니다.
* 플랫폼에 독립적이다 - 모든 IDEs에 설치 할수 있으며, 사람들은 gulp를 PHP, .NET, Node.js, Java 및 다른 플랫폼에 사용합니다.
* 강력한 생태계 - 사용하려는 npm 모듈을 사용할수 있고, 2000개 이상의 선별된 특별한 파일 스트림 변환을 사용한 플러그인이 있습니다.
* 간단함 - 간단한 API 환경을 제공함으로서, gulp는 쉽게 배우고 간단하게 사용합니다.

간단하게 정리해 보자면, 어디서든 쓸수 있고, 간단하며 많은 플러그인을 가진 자동화 툴이란 이야기이다.


# How to use gulp?

gulp의 사용법을 비교해보자면 make를 들어볼 수 있다.(이런 이유로 앞에 설명하였다;) makefile이라는 명세를 만들고 make 명령어를 실행하는 방법이다. make와 같이 gulp도 gulpfile.js라는 파일에 task를 명세하여 사용한다.

# hello gulp world!!

gulp를 설치하는 방법은 간단하다. (npm은 설치되어있다고 가정합니다. 만약 모른다면? [여기로!](https://github.com/npm/npm#super-easy-install))

    npm install --global gulp-cli

또한 `gulp`라는 명령어를 사용하기 위해 프로젝트 디렉토리에서 아래의 명령어를 처주기만 하면된다.

    npm install --save-dev gulp

그럼 프로젝트 디렉토리에서 gulpfile.js(이하 gulpfile)만 작성하면 준비가 끝난다.

```
var gulp = require('gulp');

gulp.task('default', function() {
  // place code for your default task here
});
```

그리고 콘솔창에서 `gulp`를 입력하면 실행한다.

## [API](https://github.com/preco21/gulp-docs-ko/blob/master/API.md)

gulp를 사용하기 위해선 몇가지 알아야할 API와 함수가 존재한다.

### [gulp.src(globs[, options])](https://github.com/preco21/gulp-docs-ko/blob/master/API.md#gulpsrcglobs-options)

```
gulp.src('client/templates/*.jade')
  .pipe(jade())
  .pipe(minify())
  .pipe(gulp.dest('build/minified_templates'));
```

`globs`는 [node-glob문법](https://github.com/isaacs/node-glob)을 따른다.

gulp는 특이한 파일 시스탬을 사용하는데 이름은 [vinyl](https://github.com/wearefractal/vinyl-fs)이라고 한다.(차후 설명 - [관련링크](http://programmingsummaries.tistory.com/382)) 이 특수한 파일시스탬은 [node의 stream](https://nodejs.org/api/stream.html)을 반환하며, node의 [pipe()](http://nodejs.org/api/stream.html#stream_readable_pipe_destination_options)를 이용하여 작업을 진행한다. 

options는 javascript 객체이며 다음의 값을 갖는다.

#### options.buffer

타입: Boolean 기본값: true

false로 지정하면 file.contents를 버퍼 파일 대신 스트림 형식으로 반환한다. 아주 큰 파일을 다룰 때 유용하게 사용할 수 있다.

참고: 플러그인은 스트림에 대한 구현을 지원하지 않을 수 있다.

#### options.read

타입: Boolean 기본값: true

false로 지정하면 file.contents가 null이 되며 모든 파일을 읽지 않는다.

#### options.base

타입: String 기본값: 모든 glob가 시작되는 위치 ([glob2base](https://github.com/wearefractal/glob2base)를 참고)

예제: somefile.js가 client/js/somedir안에 있을 때:

```
gulp.src('client/js/**/*.js') // Matches 'client/js/somedir/somefile.js' and resolves `base` to `client/js/`
  .pipe(minify())
  .pipe(gulp.dest('build'));  // Writes 'build/somedir/somefile.js'

gulp.src('client/js/**/*.js', { base: 'client' })
  .pipe(minify())
  .pipe(gulp.dest('build'));  // Writes 'build/js/somedir/somefile.js'
```

### [gulp.dest(path[, options])](https://github.com/preco21/gulp-docs-ko/blob/master/API.md#gulpdestpath-options)

파이프된 스트림을 파일로 변환시킨다. 파일로 변환 후 스트림은 끝나지 않으니 여러번 사용할 수 있으며, 지정된 경로에 폴더가 없을경우 자동으로 생성한다.

```
gulp.src('./client/templates/*.jade')
  .pipe(jade())
  .pipe(gulp.dest('./build/templates'))
  .pipe(minify())
  .pipe(gulp.dest('./build/minified_templates'));
```

options는 javascript 객체이며 다음의 값을 갖는다.

#### options.cwd

타입: String 기본값: process.cwd()

결과물을 출력할 cwd 폴더를 지정합니다. 출력 폴더가 상대 경로일 경우에만 작동한다.

#### options.mode

타입: String 기본값: 0777

출력 폴더를 생성하기 위해 필요한 폴더의 모드를 8진수 권한 문자열로 설정한다.


### [gulp.task(name[, deps], fn)](https://github.com/preco21/gulp-docs-ko/blob/master/API.md#gulptaskname-deps-fn)

[Orchestrator](https://github.com/robrich/orchestrator)를 사용하여 구현된 이 API는 아래의 예시 처럼 사용하면 된다.

```
gulp.task('mytask', ['array', 'of', 'task', 'names'], function() {
  // do stuff
  gulp.src('client/templates/*.jade')
  .pipe(jade())
  .pipe(minify())
  .pipe(gulp.dest('build/minified_templates'));
});
```

위의 작업은 `/client/templates/`안의 jade파일을 gulp-jade플러그인을 이용하여 컴파일 하고, minifying 후 `build/minified_templates`경로에 저장하는 하나의 task다.

function의 작업을 비동기로 만들 수도 있다.

#### 콜백 사용
```
// run a command in a shell
var exec = require('child_process').exec;
gulp.task('jekyll', function(cb) {
  // build Jekyll
  exec('jekyll build', function(err) {
    if (err) return cb(err); // return error
    cb(); // finished task
  });
});
```

#### Stream 반환
```
gulp.task('somename', function() {
  var stream = gulp.src('client/**/*.js')
    .pipe(minify())
    .pipe(gulp.dest('build'));
  return stream;
});
```

#### Promise 반환
```
var Q = require('q');

gulp.task('somename', function() {
  var deferred = Q.defer();

  // do async stuff
  setTimeout(function() {
    deferred.resolve();
  }, 1);

  return deferred.promise;
});
```

위의 비동기 작업들은 기본적으로 모든 task는 한 번에 실행된다. 하지만 아래의 규칙을 적용한다면 비동기이지만 순차적으로 실행이 가능하다.
* 작업이 언제 완료되는지 표시한다.
* 다른 작업에서 해당 작업의 완료에 대해 표시해야 한다.

예제를 통해서 알아봅시다.

```
var gulp = require('gulp');

// 작업이 완료 될 때 엔진이 알 수 있도록 콜백을 받는다.
gulp.task('one', function(cb) {
    // do stuff -- async or otherwise
    cb(err); // if err is not null and not undefined, the run will stop, and note that it failed
});

// identifies a dependent task must be complete before this one begins
// 이 작업이 시작되기 전에 종속된 작업(one)이 완료되어야 한다.
gulp.task('two', ['one'], function() {
    // task 'one' is done now
});

gulp.task('default', ['one', 'two']);
```

먼저 "one"과 "two"라는 작업이 있고, 다음과 같은 순서에 맞춰 진행 한다:

1. Stream, 콜백 또는 Promise을 사용하여 작업 "one"이 정상적으로 완료되는 부분을 표시한다.

2. 작업 "two"에선 실행 이전에 필요한 "one"이라는 작업을 종속성 작업으로 지정한다.

### gulp.watch(glob [, opts], {two way method})

gulp.watch는 원하는 폴더를 감시하고 해당 폴더의 내용이 변경되었을 때 무언가를 진행하기 위해 만들어진 API다. 총 2가지 방법으로 사용할 수 있다.

우선 2가지의 변수를 설명합니다.

#### glob

타입: String 또는 Array

변경을 감시할 타겟 파일입니다. 단일 glob 또는 배열을 지정할 수 있다.

#### opts

타입: Object

gaze로 넘겨지는 옵션이다.

### [gulp.watch(glob [, opts], tasks)](https://github.com/preco21/gulp-docs-ko/blob/master/API.md#gulpwatchglob-opts-tasks)

```
var watcher = gulp.watch('js/**/*.js', ['uglify','reload']);
watcher.on('change', function(event) {
  console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
});
```

#### tasks

타입: Array

파일의 변경 이벤트가 발생할 때마다 호출할 task다. 여러 개 지정할 수 있다.

### [gulp.watch(glob[, opts, cb])](https://github.com/preco21/gulp-docs-ko/blob/master/API.md#gulpwatchglob-opts-cb)

```
gulp.watch('js/**/*.js', function(event) {
  console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
});
```

#### cb(event)

타입: Function

콜백은 변경 이벤트마다 호출되며 event 객체를 반환한다.

#### event.type

타입: String

발생한 이벤트의 타입입니다. added, changed, deleted 중 한 가지가 지정된다.

#### event.path

타입: String

이벤트가 발생한 파일의 경로다.



