# api documentation for  [css-element-queries (v0.4.0)](https://github.com/marcj/css-element-queries)  [![npm package](https://img.shields.io/npm/v/npmdoc-css-element-queries.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-css-element-queries) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-css-element-queries.svg)](https://travis-ci.org/npmdoc/node-npmdoc-css-element-queries)
#### CSS-Element-Queries Polyfill. proof-of-concept for high-speed element dimension/media queries in valid css.

[![NPM](https://nodei.co/npm/css-element-queries.png?downloads=true)](https://www.npmjs.com/package/css-element-queries)

[![apidoc](https://npmdoc.github.io/node-npmdoc-css-element-queries/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-css-element-queries_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-css-element-queries/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-css-element-queries/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-css-element-queries/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Marc J. Schmidt"
    },
    "bugs": {
        "url": "https://github.com/marcj/css-element-queries/issues"
    },
    "dependencies": {},
    "description": "CSS-Element-Queries Polyfill. proof-of-concept for high-speed element dimension/media queries in valid css.",
    "devDependencies": {
        "grunt": "^0.4.5",
        "grunt-bump": "^0.3.1"
    },
    "directories": {
        "test": "test"
    },
    "dist": {
        "shasum": "cb5bcde9d980ac3699e7471f798e70600088d3de",
        "tarball": "https://registry.npmjs.org/css-element-queries/-/css-element-queries-0.4.0.tgz"
    },
    "gitHead": "e48168cc3839fbdf365bdc5f71813d94f0502d7a",
    "homepage": "https://github.com/marcj/css-element-queries",
    "license": "MIT",
    "main": "index.js",
    "maintainers": [
        {
            "name": "marcj",
            "email": "marc@marcjschmidt.de"
        }
    ],
    "name": "css-element-queries",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+ssh://git@github.com/marcj/css-element-queries.git"
    },
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "version": "0.4.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module css-element-queries](#apidoc.module.css-element-queries)
1.  [function <span class="apidocSignatureSpan">css-element-queries.</span>ElementQueries ()](#apidoc.element.css-element-queries.ElementQueries)
1.  object <span class="apidocSignatureSpan">css-element-queries.</span>ResizeSensor

#### [module css-element-queries.ElementQueries](#apidoc.module.css-element-queries.ElementQueries)
1.  boolean <span class="apidocSignatureSpan">css-element-queries.ElementQueries.</span>withTracking
1.  [function <span class="apidocSignatureSpan">css-element-queries.</span>ElementQueries ()](#apidoc.element.css-element-queries.ElementQueries.ElementQueries)
1.  [function <span class="apidocSignatureSpan">css-element-queries.ElementQueries.</span>detach (element)](#apidoc.element.css-element-queries.ElementQueries.detach)
1.  [function <span class="apidocSignatureSpan">css-element-queries.ElementQueries.</span>init ()](#apidoc.element.css-element-queries.ElementQueries.init)
1.  [function <span class="apidocSignatureSpan">css-element-queries.ElementQueries.</span>listen ()](#apidoc.element.css-element-queries.ElementQueries.listen)
1.  [function <span class="apidocSignatureSpan">css-element-queries.ElementQueries.</span>update (withTracking)](#apidoc.element.css-element-queries.ElementQueries.update)



# <a name="apidoc.module.css-element-queries"></a>[module css-element-queries](#apidoc.module.css-element-queries)

#### <a name="apidoc.element.css-element-queries.ElementQueries"></a>[function <span class="apidocSignatureSpan">css-element-queries.</span>ElementQueries ()](#apidoc.element.css-element-queries.ElementQueries)
- description and source-code
```javascript
ElementQueries = function () {

    var trackingActive = false;
    var elements = [];

<span class="apidocCodeCommentSpan">    /**
     *
     * @param element
     * @returns {Number}
     */
</span>    function getEmSize(element) {
        if (!element) {
            element = document.documentElement;
        }
        var fontSize = window.getComputedStyle(element, null).fontSize;
        return parseFloat(fontSize) || 16;
    }

    /**
     *
     * @copyright https://github.com/Mr0grog/element-query/blob/master/LICENSE
     *
     * @param {HTMLElement} element
     * @param {*} value
     * @returns {*}
     */
    function convertToPx(element, value) {
        var numbers = value.split(/\d/);
        var units = numbers[numbers.length-1];
        value = parseFloat(value);
        switch (units) {
            case "px":
                return value;
            case "em":
                return value * getEmSize(element);
            case "rem":
                return value * getEmSize();
            // Viewport units!
            // According to http://quirksmode.org/mobile/tableViewport.html
            // documentElement.clientWidth/Height gets us the most reliable info
            case "vw":
                return value * document.documentElement.clientWidth / 100;
            case "vh":
                return value * document.documentElement.clientHeight / 100;
            case "vmin":
            case "vmax":
                var vw = document.documentElement.clientWidth / 100;
                var vh = document.documentElement.clientHeight / 100;
                var chooser = Math[units === "vmin" ? "min" : "max"];
                return value * chooser(vw, vh);
            default:
                return value;
            // for now, not supporting physical units (since they are just a set number of px)
            // or ex/ch (getting accurate measurements is hard)
        }
    }

    /**
     *
     * @param {HTMLElement} element
     * @constructor
     */
    function SetupInformation(element) {
        this.element = element;
        this.options = {};
        var key, option, width = 0, height = 0, value, actualValue, attrValues, attrValue, attrName;

        /**
         * @param {Object} option {mode: 'min|max', property: 'width|height', value: '123px'}
         */
        this.addOption = function(option) {
            var idx = [option.mode, option.property, option.value].join(',');
            this.options[idx] = option;
        };

        var attributes = ['min-width', 'min-height', 'max-width', 'max-height'];

        /**
         * Extracts the computed width/height and sets to min/max- attribute.
         */
        this.call = function() {
            // extract current dimensions
            width = this.element.offsetWidth;
            height = this.element.offsetHeight;

            attrValues = {};

            for (key in this.options) {
                if (!this.options.hasOwnProperty(key)){
                    continue;
                }
                option = this.options[key];

                value = convertToPx(this.element, option.value);

                actualValue = option.property == 'width' ? width : height;
                attrName = option.mode + '-' + option.property;
                attrValue = '';

                if (option.mode == 'min' && actualValue >= value) {
                    attrValue += option.value;
                }

                if (option.mode == 'max' && actualValue <= value) {
                    attrValue += option.value;
                }

                if (!attrValues[attrName]) attrValues[attrName] = '';
                if (attrValue && -1 === (' '+attrValues[attrName]+' ').indexOf(' ' + attrValue + ' ')) {
                    attrValues[attrName] += ' ' + attrValue;
                }
            }

            for (var k in attributes) {
                if(!attributes.hasOwnProperty(k)) continue;

                if (attrValues[attributes[k]]) {
                    this.element.setAttribute(attributes[k], attrValues[attributes[k]].substr(1));
                } else {
                    this.ele ...
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.css-element-queries.ElementQueries"></a>[module css-element-queries.ElementQueries](#apidoc.module.css-element-queries.ElementQueries)

#### <a name="apidoc.element.css-element-queries.ElementQueries.ElementQueries"></a>[function <span class="apidocSignatureSpan">css-element-queries.</span>ElementQueries ()](#apidoc.element.css-element-queries.ElementQueries.ElementQueries)
- description and source-code
```javascript
ElementQueries = function () {

    var trackingActive = false;
    var elements = [];

<span class="apidocCodeCommentSpan">    /**
     *
     * @param element
     * @returns {Number}
     */
</span>    function getEmSize(element) {
        if (!element) {
            element = document.documentElement;
        }
        var fontSize = window.getComputedStyle(element, null).fontSize;
        return parseFloat(fontSize) || 16;
    }

    /**
     *
     * @copyright https://github.com/Mr0grog/element-query/blob/master/LICENSE
     *
     * @param {HTMLElement} element
     * @param {*} value
     * @returns {*}
     */
    function convertToPx(element, value) {
        var numbers = value.split(/\d/);
        var units = numbers[numbers.length-1];
        value = parseFloat(value);
        switch (units) {
            case "px":
                return value;
            case "em":
                return value * getEmSize(element);
            case "rem":
                return value * getEmSize();
            // Viewport units!
            // According to http://quirksmode.org/mobile/tableViewport.html
            // documentElement.clientWidth/Height gets us the most reliable info
            case "vw":
                return value * document.documentElement.clientWidth / 100;
            case "vh":
                return value * document.documentElement.clientHeight / 100;
            case "vmin":
            case "vmax":
                var vw = document.documentElement.clientWidth / 100;
                var vh = document.documentElement.clientHeight / 100;
                var chooser = Math[units === "vmin" ? "min" : "max"];
                return value * chooser(vw, vh);
            default:
                return value;
            // for now, not supporting physical units (since they are just a set number of px)
            // or ex/ch (getting accurate measurements is hard)
        }
    }

    /**
     *
     * @param {HTMLElement} element
     * @constructor
     */
    function SetupInformation(element) {
        this.element = element;
        this.options = {};
        var key, option, width = 0, height = 0, value, actualValue, attrValues, attrValue, attrName;

        /**
         * @param {Object} option {mode: 'min|max', property: 'width|height', value: '123px'}
         */
        this.addOption = function(option) {
            var idx = [option.mode, option.property, option.value].join(',');
            this.options[idx] = option;
        };

        var attributes = ['min-width', 'min-height', 'max-width', 'max-height'];

        /**
         * Extracts the computed width/height and sets to min/max- attribute.
         */
        this.call = function() {
            // extract current dimensions
            width = this.element.offsetWidth;
            height = this.element.offsetHeight;

            attrValues = {};

            for (key in this.options) {
                if (!this.options.hasOwnProperty(key)){
                    continue;
                }
                option = this.options[key];

                value = convertToPx(this.element, option.value);

                actualValue = option.property == 'width' ? width : height;
                attrName = option.mode + '-' + option.property;
                attrValue = '';

                if (option.mode == 'min' && actualValue >= value) {
                    attrValue += option.value;
                }

                if (option.mode == 'max' && actualValue <= value) {
                    attrValue += option.value;
                }

                if (!attrValues[attrName]) attrValues[attrName] = '';
                if (attrValue && -1 === (' '+attrValues[attrName]+' ').indexOf(' ' + attrValue + ' ')) {
                    attrValues[attrName] += ' ' + attrValue;
                }
            }

            for (var k in attributes) {
                if(!attributes.hasOwnProperty(k)) continue;

                if (attrValues[attributes[k]]) {
                    this.element.setAttribute(attributes[k], attrValues[attributes[k]].substr(1));
                } else {
                    this.ele ...
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.css-element-queries.ElementQueries.detach"></a>[function <span class="apidocSignatureSpan">css-element-queries.ElementQueries.</span>detach (element)](#apidoc.element.css-element-queries.ElementQueries.detach)
- description and source-code
```javascript
detach = function (element) {
    if (element.elementQueriesSetupInformation) {
        //element queries
        element.elementQueriesSensor.detach();
        delete element.elementQueriesSetupInformation;
        delete element.elementQueriesSensor;

    } else if (element.resizeSensor) {
        //responsive image

        element.resizeSensor.detach();
        delete element.resizeSensor;
    } else {
        //console.log('detached already', element);
    }
}
```
- example usage
```shell
...
    }

    forEachElement(element, function(elem){
        attachResizeEvent(elem, callback);
    });

    this.detach = function(ev) {
        ResizeSensor.detach(element, ev);
    };
};

ResizeSensor.detach = function(element, ev) {
    forEachElement(element, function(elem){
        if(elem.resizedAttached && typeof ev == "function"){
            elem.resizedAttached.remove(ev);
...
```

#### <a name="apidoc.element.css-element-queries.ElementQueries.init"></a>[function <span class="apidocSignatureSpan">css-element-queries.ElementQueries.</span>init ()](#apidoc.element.css-element-queries.ElementQueries.init)
- description and source-code
```javascript
init = function () {
    if (!ElementQueries.instance) {
        ElementQueries.instance = new ElementQueries();
    }

    ElementQueries.instance.init(ElementQueries.withTracking);
}
```
- example usage
```shell
...

//attaches to DOMLoadContent
EQ.listen();

//or if you want to trigger it yourself.
// Parse all available CSS and attach ResizeSensor to those elements which have rules attached
// (make sure this is called after 'load' event, because CSS files are not ready when domReady is fired.
EQ.init();
'''

## Issues

- So far does not work on 'img' and other elements that can't contain other elements. Wrapping with a 'div' works fine though (See
 demo).
- Adds additional hidden elements into selected target element and forces target element to be relative or absolute.
- Local stylesheets do not work (using 'file://' protocol).
...
```

#### <a name="apidoc.element.css-element-queries.ElementQueries.listen"></a>[function <span class="apidocSignatureSpan">css-element-queries.ElementQueries.</span>listen ()](#apidoc.element.css-element-queries.ElementQueries.listen)
- description and source-code
```javascript
listen = function () {
    domLoaded(ElementQueries.init);
}
```
- example usage
```shell
...

If you're using a module loader you need to trigger the event listening or initialization yourself:

'''javascript
var EQ = require('node_modules/css-element-queries/ElementQueries');

 //attaches to DOMLoadContent
EQ.listen();

//or if you want to trigger it yourself.
// Parse all available CSS and attach ResizeSensor to those elements which have rules attached
// (make sure this is called after 'load' event, because CSS files are not ready when domReady is fired.
EQ.init();
'''
...
```

#### <a name="apidoc.element.css-element-queries.ElementQueries.update"></a>[function <span class="apidocSignatureSpan">css-element-queries.ElementQueries.</span>update (withTracking)](#apidoc.element.css-element-queries.ElementQueries.update)
- description and source-code
```javascript
update = function (withTracking) {
    ElementQueries.instance.update(withTracking);
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
