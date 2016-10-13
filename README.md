# angularstart

.NET core backend
Angular 2 front end with typescript

theres multiple ways to set up the client
theres multiple ways to set up the server
this makes even more multiple ways to set up combination of both.

We will choose one way

Pre-reqs:
Visual Studio 2015 Update 3
.NET core 1.0.1


1) File > New .NET core Web API project
2) Set up to serve static files
   - include `"Microsoft.AspNetCore.StaticFiles": "1.0.0`
   - `app.UseStaticFiles();`
3) handle urls picked up by asp.net as 404s and just serve index.html

```
app.Use(async (context, next) =>
{
    await next();

    if (context.Response.StatusCode == 404
        && !Path.HasExtension(context.Request.Path.Value))
    {
        context.Request.Path = "/index.html";
        await next();
    }
});
```

4) edit F5 settings to not be annoying
   - Properties > launchsettings.json

5) run a simple controller
   - `/api/values/`

6) add a package.json to project and run `npm install`

```
{
  "name": "<project name>",
  "version": "1.0.0",
  "scripts": {
    "start": "tsc && concurrently \"tsc -w\" \"lite-server\" ",
    "lite": "lite-server",
    "postinstall": "typings install gulp restore",
    "tsc": "tsc",
    "tsc:w": "tsc -w",
    "typings": "typings"
  },
  "licenses": [
    {
      "type": "MIT",
      "url": "https://github.com/angular/angular.io/blob/master/LICENSE"
    }
  ],
  "dependencies": {
    "@angular/common": "2.0.2",
    "@angular/compiler": "2.0.2",
    "@angular/core": "2.0.2",
    "@angular/forms": "2.0.2",
    "@angular/http": "2.0.2",
    "@angular/platform-browser": "2.0.2",
    "@angular/platform-browser-dynamic": "2.0.2",
    "@angular/router": "3.0.2",
    "@angular/upgrade": "2.0.2",
    "angular-in-memory-web-api": "0.1.5",
    "bootstrap": "3.3.7",
    "core-js": "2.4.1",
    "reflect-metadata": "0.1.8",
    "rxjs": "5.0.0-beta.12",
    "systemjs": "0.19.39",
    "zone.js": "0.6.25"
  },
  "devDependencies": {
    "concurrently": "3.0.0",
    "lite-server": "2.2.2",
    "gulp": "^3.9.1",
    "typescript": "2.0.3",
    "typings":"1.4.0"
  }
}
```


7) add new gulpfile.js to move all our packages from node_modules into the wwwroot/libs

```
var gulp = require('gulp');

var libs = './wwwroot/libs/';

gulp.task('default', function () {
    // place code for your default task here
});

gulp.task('restore:core-js', function() {
    gulp.src([
        'node_modules/core-js/client/*.js'
    ]).pipe(gulp.dest(libs + 'core-js'));
});
gulp.task('restore:zone.js', function () {
    gulp.src([
        'node_modules/zone.js/dist/*.js'
    ]).pipe(gulp.dest(libs + 'zone.js'));
});
gulp.task('restore:reflect-metadata', function () {
    gulp.src([
        'node_modules/reflect-metadata/reflect.js'
    ]).pipe(gulp.dest(libs + 'reflect-metadata'));
});
gulp.task('restore:systemjs', function () {
    gulp.src([
        'node_modules/systemjs/dist/*.js'
    ]).pipe(gulp.dest(libs + 'systemjs'));
});
gulp.task('restore:rxjs', function () {
    gulp.src([
        'node_modules/rxjs/**/*.js'
    ]).pipe(gulp.dest(libs + 'rxjs'));
});
gulp.task('restore:angular-in-memory-web-api', function () {
    gulp.src([
        'node_modules/angular-in-memory-web-api/**/*.js'
    ]).pipe(gulp.dest(libs + 'angular-in-memory-web-api'));
});

gulp.task('restore:angular', function () {
    gulp.src([
        'node_modules/@angular/**/*.js'
    ]).pipe(gulp.dest(libs + '@angular'));
});

gulp.task('restore:bootstrap', function () {
    gulp.src([
        'node_modules/bootstrap/dist/**/*.*'
    ]).pipe(gulp.dest(libs + 'bootstrap'));
});

gulp.task('restore', [
    'restore:core-js',
    'restore:zone.js',
    'restore:reflect-metadata',
    'restore:systemjs',
    'restore:rxjs',
    'restore:angular-in-memory-web-api',
    'restore:angular',
    'restore:bootstrap'
]);
```


8) add a typings.json to set up typescript libraries

```
{
  "globalDependencies": {
    "core-js": "registry:dt/core-js#0.0.0+20160725163759",
    "jasmine": "registry:dt/jasmine#2.2.0+20160621224255",
    "node": "registry:dt/node#6.0.0+20160909174046"
  }
}
```

9) add tsconfig.json file

```
{
  "compileOnSave": true,
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "moduleResolution": "node",
    "sourceMap": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "removeComments": false,
    "noImplicitAny": false
  },
  "exclude": [
    "node_modules"
  ]
}
```

10) add a .gitignore

```
*.js
*.map
wwwroot/libs
node_modules
typings
bin
obj
```

11) add wwwroot/index.html

```
<html>
<head>
    <title>AngularStart</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="styles.css">
    <script src="libs/core-js/shim.min.js"></script>
    <script src="libs/zone.js/zone.js"></script>
    <script src="libs/reflect-metadata/Reflect.js"></script>
    <script src="libs/systemjs/system.src.js"></script>
    <script src="systemjs.config.js"></script>
    <script>
        System.import('app').catch(function (err) { console.error(err); });
    </script>
</head>
<body>
    <the-app>Loading...</the-app>
</body>
</html>
```

12) add wwwroot/systemjs.config.js for DI for angular app

```
(function (global) {
    System.config({
        paths: {
            // paths serve as alias
            'npm:': 'libs/'
        },
        // map tells the System loader where to look for things
        map: {
            // our app is within the app folder
            app: 'app',
            // angular bundles
            '@angular/core': 'npm:@angular/core/bundles/core.umd.js',
            '@angular/common': 'npm:@angular/common/bundles/common.umd.js',
            '@angular/compiler': 'npm:@angular/compiler/bundles/compiler.umd.js',
            '@angular/platform-browser': 'npm:@angular/platform-browser/bundles/platform-browser.umd.js',
            '@angular/platform-browser-dynamic': 'npm:@angular/platform-browser-dynamic/bundles/platform-browser-dynamic.umd.js',
            '@angular/http': 'npm:@angular/http/bundles/http.umd.js',
            '@angular/router': 'npm:@angular/router/bundles/router.umd.js',
            '@angular/forms': 'npm:@angular/forms/bundles/forms.umd.js',
            // other libraries
            'rxjs': 'npm:rxjs',
            'angular-in-memory-web-api': 'npm:angular-in-memory-web-api',
        },
        // packages tells the System loader how to load when no filename and/or no extension
        packages: {
            app: {
                main: './main.js',
                defaultExtension: 'js'
            },
            rxjs: {
                defaultExtension: 'js'
            },
            'angular-in-memory-web-api': {
                main: './index.js',
                defaultExtension: 'js'
            }
        }
    });
})(this);
```


13) add wwwroot/app/main.ts to bootstrap our app 

```
import {platformBrowserDynamic} from '@angular/platform-browser-dynamic';
import {AppModule} from './app.module';

platformBrowserDynamic().bootstrapModule(AppModule);
```

14) add wwwroot/app/app.module.ts to declare the app

```
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent }   from './app.component';

@NgModule({
    imports:      [ BrowserModule ],
    declarations: [ AppComponent ],
    bootstrap:    [ AppComponent ]
})
export class AppModule { }
```


15) add wwwroot/app/app.component.ts to add first component to the app

```
import { Component } from '@angular/core';

@Component({
    selector: 'the-app',
    template: '<h1>My First Angular App</h1>'
})
export class AppComponent { }
```

16) add styles.css to wwwroot to be served statically

17) typings install, npm start, gulp restore

18) browse to root of website to lauch the index.html

