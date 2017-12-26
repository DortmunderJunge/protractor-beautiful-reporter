# Angularized HTML Reporter with Screenshots for Protractor

![HTML / Angular Test Report](https://raw.githubusercontent.com/Evilweed/protractor-beautiful-reporter/master/images/html-report.png "test report")

## IMPORTANT !
* This reporter works with Node version 7.6 or later
* Jasmine 1 is no longer supported

## Features
* Browser's Logs (only for Chrome)
* Stack Trace (with suspected line highlight)
* Screenshot
* Screenshot only on failed spec
* Search
* Filters (can display only Passed/Failed/Pending/Has Browser Logs)
* Inline Screenshots
* Details (Browser/Session ID/OS)
* Duration time for test cases (only Jasmine2)

## Wish list
* HTML Dump

Need some feature? Let me know or code it and propose Pull Request :)

## Props

This is built on top of [protractor-angular-screenshot-reporter](https://github.com/bcole/protractor-angular-screenshot-reporter), which is built on top of [protractor-html-screenshot-reporter](https://github.com/jintoppy/protractor-html-screenshot-reporter), which is built on top of [protractor-screenshot-reporter](https://github.com/swissmanu/protractor-screenshot-reporter).

`protractor-beautiful-reporter` still generates a HTML report, but it is Angular-based and improves on the original formatting.


## Usage
The `protractor-beautiful-reporter` module is available via npm:

```bash
$ npm install protractor-beautiful-reporter --save-dev
```

In your Protractor configuration file, register `protractor-beautiful-reporter` in Jasmine.

#### Jasmine 1.x:

```javascript
No longer supported
```

#### Jasmine 2.x:
Jasmine 2.x introduced changes to reporting that are not backwards compatible.  To use `protractor-beautiful-reporter` with Jasmine 2, please make sure to use the **`getJasmine2Reporter()`** compatibility method introduced in `protractor-beautiful-reporter@0.1.0`.

```javascript
var HtmlReporter = require('protractor-beautiful-reporter');

exports.config = {
   // your config here ...

   onPrepare: function() {
      // Add a screenshot reporter and store screenshots to `/tmp/screenshots`:
      jasmine.getEnv().addReporter(new HtmlReporter({
         baseDirectory: 'tmp/screenshots'
      }).getJasmine2Reporter());
   }
}
```

## Configuration
### Base Directory (mandatory)
You have to pass a directory path as parameter when creating a new instance of
the screenshot reporter:

```javascript
var reporter = new HtmlReporter({
   baseDirectory: 'tmp/screenshots'
});
```

If the given directory does not exists, it is created automatically as soon as a screenshot needs to be stored.

### Path Builder (optional)
The function passed as second argument to the constructor is used to build up paths for screenshot files:

```javascript
var path = require('path');

new HtmlReporter({
   baseDirectory: 'tmp/screenshots'
   , pathBuilder: function pathBuilder(spec, descriptions, results, capabilities) {
      // Return '<browser>/<specname>' as path for screenshots:
      // Example: 'firefox/list-should work'.
      return path.join(capabilities.caps_.browser, descriptions.join('-'));
   }
});
```
If you omit the path builder, a [GUID](http://en.wikipedia.org/wiki/Globally_Unique_Identifier) is used by default instead.

Caution: The format/structure of these parameters (spec, descriptions, results, capabilities) differs between Jasmine 2.x and Jasmine 1.x.


### Meta Data Builder (optional)
You can modify the contents of the JSON meta data file by passing a function `metaDataBuilder` function as third constructor parameter:

```javascript
new HtmlReporter({
   baseDirectory: 'tmp/screenshots'
   , metaDataBuilder: function metaDataBuilder(spec, descriptions, results, capabilities) {
      // Return the description of the spec and if it has passed or not:
      return {
         description: descriptions.join(' ')
         , passed: results.passed()
      };
   }
});
```

### Screenshots Subfolder (optional)
You can store all images in subfolder by using `screenshotsSubfolder` option:

```javascript
new HtmlReporter({
   baseDirectory: 'tmp/screenshots'
   , screenshotsSubfolder: 'images'
});
```

If you omit this, all images will be stored in main folder.


### JSONs Subfolder (optional)
You can store all JSONs in subfolder by using `jsonsSubfolder` option:

```javascript
new HtmlReporter({
   baseDirectory: 'tmp/screenshots'
   , jsonsSubfolder: 'jsons'
});
```

If you omit this, all images will be stored in main folder.


### Sort function (optional)
You can change default `sortFunction` option:

```javascript
new HtmlReporter({
   baseDirectory: 'tmp/screenshots'
   , sortFunction: function sortFunction(a, b) {
         if (a.cachedBase === undefined) {
             var aTemp = a.description.split('|').reverse();
             a.cachedBase = aTemp.slice(0).slice(0,-1);
             a.cachedName = aTemp.slice(0).join('');
         };
         if (b.cachedBase === undefined) {
             var bTemp = b.description.split('|').reverse();
             b.cachedBase = bTemp.slice(0).slice(0,-1);
             b.cachedName = bTemp.slice(0).join('');
         };
    
         var firstBase = a.cachedBase;
         var secondBase = b.cachedBase;
    
         for (var i = 0; i < firstBase.length || i < secondBase.length; i++) {
    
             if (firstBase[i] === undefined) { return -1; }
             if (secondBase[i] === undefined) { return 1; }
             if (firstBase[i].localeCompare(secondBase[i]) === 0) { continue; }
             return firstBase[i].localeCompare(secondBase[i]);
         }
    
         var firstTimestamp = a.timestamp;
         var secondTimestamp = b.timestamp;
    
         if(firstTimestamp < secondTimestamp) return -1;
         else return 1;
     }
});
```

If you omit this, all specs will be sorted by timestamp (please be aware that sharded runs look ugly when sorted by default sort).


### Report for skipped test cases (optional)
You can set `excludeSkipped` to `true` to exclude reporting skipped test cases entirely.

```javascript
new HtmlReporter({
  baseDirectory: `tmp/screenshots`
  , excludeSkipped: true
});
```
Default is `false`.

### Screenshots for skipped test cases (optional)

You can define if you want report screenshots from skipped test cases using the `takeScreenShotsForSkippedSpecs` option:

```javascript
new HtmlReporter({
   baseDirectory: 'tmp/screenshots'
   , takeScreenShotsForSkippedSpecs: true
});
```
Default is `false`.

### Screenshots only for failed test cases (optional)
 Also you can define if you want capture screenshots only from failed test cases using the `takeScreenShotsOnlyForFailedSpecs:` option:
 
 ```javascript
 new HtmlReporter({
    baseDirectory: 'tmp/screenshots'
    , takeScreenShotsOnlyForFailedSpecs: true
 });
 ```
 If you set the value to `true`, the reporter for the passed test will still be generated, but, there will be no screenshot.
 
 Default is `false`.


### Add title for the html report (optional)
 Also you can define a document title for the html report generated using the `docTitle:` option:
 
 ```javascript
 new HtmlReporter({
    baseDirectory: 'tmp/screenshots'
    , docTitle: 'my reporter'
 });
 ```

Default is `Generated test report`.

### Change html report file name (optional)
 Also you can change document name for the html report generated using the `docName:` option:
 
 ```javascript
 new HtmlReporter({
    baseDirectory: 'tmp/screenshots'
    , docName: 'index.html'
 });
 ```
Default is `report.html`.

### Option to override CSS file used in reporter (optional)
 You can change stylesheet used for the html report generated using the `cssOverrideFile:` option:
 
 ```javascript
 new HtmlReporter({
    baseDirectory: 'tmp/screenshots'
    , cssOverrideFile: 'css/style.css'
 });
 ```

### Preserve base directory (optional)
 You can preserve (or clear) the base directory using `preserveDirectory:` option:
 
 ```javascript
 new HtmlReporter({
    baseDirectory: 'tmp/screenshots'
    , preserveDirectory: false
 });
 ```
Default is `true`.


### Store Browser logs (optional)
 You can gather browser logs using `gatherBrowserLogs:` option:
 
 ```javascript
 new HtmlReporter({
    baseDirectory: 'tmp/screenshots'
    , gatherBrowserLogs: false
 });
 ```
Default is `true`.

## HTML Reporter

Upon running Protractor tests with the above config, the screenshot reporter will generate JSON and PNG files for each test.

In addition, a small HTML/Angular app is copied to the output directory, which cleanly lists the test results, any errors (with stacktraces), and screenshots.

![HTML / Angular Test Report](https://raw.githubusercontent.com/Evilweed/protractor-beautiful-reporter/master/images/html-report.png "test report")

Click More Details to see more information about the test runs.

![Click More Details](https://raw.githubusercontent.com/Evilweed/protractor-beautiful-reporter/master/images/more-details.png "more detail")

Use Search Input Field to narrow down test list.

![View Search](https://raw.githubusercontent.com/Evilweed/protractor-beautiful-reporter/master/images/search.png "view search")

Click View Stacktrace to see details of the error (if the test failed). Suspected line is highlighted.

Click View Browser Log to see Browser Log (collects browser logs also from passed tests)

![View Stacktrace](https://raw.githubusercontent.com/Evilweed/protractor-beautiful-reporter/master/images/stack-highlight.png "view stacktrace")

Click View Screenshot to see an image of the webpage at the end of the test.

![View Inline Screenshot](https://raw.githubusercontent.com/Evilweed/protractor-beautiful-reporter/master/images/screenshot.png "view inline screenshot")

Click Inline Screenshots to see an inline screenshots in HTML report.

![View Inline Screenshot](https://raw.githubusercontent.com/Evilweed/protractor-beautiful-reporter/master/images/inline-screenshot.png "view inline screenshot")

Please see the `examples` folder for sample usage. 

To run the sample, execute the following commands in the `examples` folder

```bash

$ npm install
$ protractor protractor.conf.js
```

After the test run, you can see that, a screenshots folder will be created with all the reports generated. 

## Donate
You like it? You can buy me a cup of coffee/glass of beer :) 

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=WYR7N9FQL94J6)

## License
Copyright (c) 2017 Marcin Cierpicki <zycienawalizkach@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
