     _                                 _    _____ 
    (_)                               | |  / ____|
     _   _ __ ___    ___    ___       | | | (___  
    | | | '_ ` _ \  / __|  / __|  _   | |  \___ \ 
    | | | | | | | | \__ \ | (__  | |__| |  ____) |
    |_| |_| |_| |_| |___/  \___|  \____/  |_____/ 
                                                  
[![npm version](https://badge.fury.io/js/imsc.svg)](https://badge.fury.io/js/imsc)

INTRODUCTION
============

imscJS is a JavaScript library for rendering IMSC1 Text and Image Profile
documents [1] to HTML5. IMSC1 is a profile of TTML [2] designed for subtitle and 
caption delivery worldwide.

[1] https://www.w3.org/TR/ttml-imsc1/  
[2] https://www.w3.org/TR/ttml1/  

A live sample web app using imscJS is available at [3].

[3] http://sandflow.com/imsc1proc/index.html

KNOWN ISSUES AND LIMITATIONS
============================

imscJS is primarily developed on Chrome. Latest versions of Firefox, Safari, 
Microsoft Internet Explorer 11 and Microsoft Edge are intended to be supported
nevertheless.

imscJS is intended to reflect the most recent published versions of TTML1 and 
IMSC1, as clarified by proposed resolutions to issues captured in their
respective bug trackers [1-2].

[1] https://github.com/w3c/ttml1/issues  
[2] https://github.com/w3c/imsc/issues  

imscJS bugs are tracked at [3]

[3] https://github.com/sandflow/imscJS/issues



RUNTIME DEPENDENCIES
====================

(required) sax-js (1.2.1) [https://www.npmjs.com/package/sax]

imscJS defines an NPM package (see DEVELOPMENT DEPENDENCIES and PACKAGE below) to simplify development, 
track dependencies and manage versions, but has no runtime dependency on node.js. Rendering to HTML5 requires a 
browser environment but parsing an IMSC1 document and tranforming it into ISDs does not.



DEVELOPMENT DEPENDENCIES
========================

(required) node.js (see package.json for a complete list of dependencies)

(recommended) git

(recommended) Netbeans 8.2

imscJS consists of a collection of modules (see MODULES below), which can be used in a node 
environment using the 'require' functionality, or standalone, in which case each module hosts its 
definitions under a global name. The modules can be combined into a single file using Browserify [1] 
to simplify use in web apps (see Gruntfile.js for an example).



QUICK START
===========

* build the 'build' target defined in Gruntfile.js using grunt [1]. This will build the library and the tests into the 
build/public_html directory, which can be served by popular web server.

[1] http://gruntjs.com/

* the resulting imsc.js and sax.js files at build/public_html/libs are, respectively, the 
imscJS library and its sax-js dependency. Both are included in a web page using the following:

```html
    <script src="libs/sax.js"></script>
    <script src="libs/imsc.js"></script>
```

See TESTS AND SAMPLES below for a list of samples available.
    

    
ARCHITECTURE
============

imscJS renders an IMSC1 document in three distinct steps:

* `fromXML(xmlstring, errorHandler, metadataHandler)` parses the document and returns a TT object. The latter contains opaque
representation of the document and exposes the method `getMediaTimeEvents()` that returns a list of time offsets (in seconds) of the
ISD, i.e. the points in time where the visual representation of the document change.

* `generateISD(tt, offset, errorHandler)` creates a canonical representation of the document (provided as a TT object generated by `fromXML()`)
at a point in time (`offset` parameter). This point in time does not have to be one of the values returned by `getMediaTimeEvents()`. For 
example, given an ISOBMFF sample covering the interval `[a, b[`, `generateISD(tt, offset, errorHandler)` would be called first with `offset = a`,
then in turn with offset set to each value of `getMediaTimeEvents()` that fall in the interval `]a, b[`.

* `renderHTML(isd, element, imgResolver, eheight, ewidth, displayForcedOnlyMode, errorHandler, previousISDState, enableRollUp)` 
renders an `isd` object returned by `generateISD()`
into a newly-created `div` element that is appended to the `element`. The `element` must be attached to the DOM.
The height and width of the child `div` element are equal to `eheight` and `ewidth` if not null, or `clientWidth` and `clientHeight` of the
parent `element` otherwise. Images URIs specified in `smpte:background` attributes are mapped to image resource URLs by the `imgResolver`
function. The latter takes the value of the `smpte:background` attribute URI and an `img` DOM element as input and is expected to
set the `src` attribute of the `img` DOM element to the absolute URI of the image. `displayForcedOnlyMode` sets the (boolean) value
 of the IMSC1 displayForcedOnlyMode parameter. `enableRollUp` enables roll-up as specified in CEA 708. `previousISDState` maintains states across
 calls, e.g. for roll-up processing.

In each step, the caller can provide an `errorHandler` to be notified of events during processing. The `errorHandler` may define four methods:
`info`, `warn`, `error` and `fatal`. Each is called with a string argument describing the event, and will cause processing to terminate if it
returns `true`.

See inline documentation for additional documentation.



MODULES
=======

The imscJS codebase is arranged in the following modules (the token between parantheses is the global name 
used if 'require' is not used to load modules):

* doc.js (imscDoc): parses an IMSC1 document into an in-memory TT object
* isd.js (imscISD): generates an ISD object from a TT object
* html.js (imscHTML): generates an HTML fragment from an ISD object
* names.js (imscNames): common constants
* styles.js (imscStyles): defines TTML styling attributes processing
* utils.js (imscUtils): common utility functions



TESTS AND SAMPLES
=================

W3C Test Suite
**************

imscJS imports the IMSC1 test suite [1] managed by the W3C as submodule at [2].
The gen-renders.html web app can be used to generate PNG renderings as as well intermediary files from these
tests. For regression testing, a copy of these intermediary files are committed at [3].

[1] https://github.com/w3c/imsc-tests  
[2] src/test/resources/imsc-tests  
[3] src/test/resources/generated  

Unit tests
**********

Unit tests are located at [1] and run as a webapp [2] using QUnit [3].

[1] src/test/webapp/js/unit-tests.js  
[2] src/test/webapp/unit_tests.html  
[3] https://qunitjs.com/  

NPM PACKAGE
===========

imscJS is released as an NPM package under the name "imsc".



NOTABLE DIRECTORIES AND FILES
=============================

|                                             |                                              |
|---------------------------------------------|----------------------------------------------|
| [/package.json](package.json)               | NPM package definition                       |
| [/Gruntfile.js](Gruntfile.js)               | Grunt build script                           |
| [/properties.json](properties.json)         | General project properties                   |
| [/README.md](README.md)                     | This file                                    |
| [/LICENSE](LICENSE)                         | License under which imscJS is made available |
| [/src/main/js](src/main/js)                 | JavaScript modules                           |
| [/src/test/resources](src/test/resources)   | Test files                                   |
| [/src/test/webapp](src/test/webapp)         | Test web applications                        |
| [/build](build)                             | Build output                                 |
