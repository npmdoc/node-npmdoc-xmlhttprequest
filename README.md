# api documentation for  [xmlhttprequest (v1.8.0)](https://github.com/driverdan/node-XMLHttpRequest)  [![npm package](https://img.shields.io/npm/v/npmdoc-xmlhttprequest.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-xmlhttprequest) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-xmlhttprequest.svg)](https://travis-ci.org/npmdoc/node-npmdoc-xmlhttprequest)
#### XMLHttpRequest for Node

[![NPM](https://nodei.co/npm/xmlhttprequest.png?downloads=true)](https://www.npmjs.com/package/xmlhttprequest)

[![apidoc](https://npmdoc.github.io/node-npmdoc-xmlhttprequest/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-xmlhttprequest_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-xmlhttprequest/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-xmlhttprequest/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-xmlhttprequest/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Dan DeFelippi",
        "url": "http://driverdan.com"
    },
    "bugs": {
        "url": "http://github.com/driverdan/node-XMLHttpRequest/issues"
    },
    "dependencies": {},
    "description": "XMLHttpRequest for Node",
    "devDependencies": {},
    "directories": {
        "lib": "./lib",
        "example": "./example"
    },
    "dist": {
        "shasum": "67fe075c5c24fef39f9d65f5f7b7fe75171968fc",
        "tarball": "https://registry.npmjs.org/xmlhttprequest/-/xmlhttprequest-1.8.0.tgz"
    },
    "engines": {
        "node": ">=0.4.0"
    },
    "gitHead": "86ff70effb6dd529b34650242b9e3b1f0b8b6e86",
    "homepage": "https://github.com/driverdan/node-XMLHttpRequest",
    "keywords": [
        "xhr",
        "ajax"
    ],
    "license": "MIT",
    "main": "./lib/XMLHttpRequest.js",
    "maintainers": [
        {
            "name": "driverdan",
            "email": "dan@driverdan.com"
        }
    ],
    "name": "xmlhttprequest",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/driverdan/node-XMLHttpRequest.git"
    },
    "scripts": {},
    "version": "1.8.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module xmlhttprequest](#apidoc.module.xmlhttprequest)
1.  [function <span class="apidocSignatureSpan">xmlhttprequest.</span>XMLHttpRequest ()](#apidoc.element.xmlhttprequest.XMLHttpRequest)



# <a name="apidoc.module.xmlhttprequest"></a>[module xmlhttprequest](#apidoc.module.xmlhttprequest)

#### <a name="apidoc.element.xmlhttprequest.XMLHttpRequest"></a>[function <span class="apidocSignatureSpan">xmlhttprequest.</span>XMLHttpRequest ()](#apidoc.element.xmlhttprequest.XMLHttpRequest)
- description and source-code
```javascript
XMLHttpRequest = function () {
  "use strict";

<span class="apidocCodeCommentSpan">  /**
   * Private variables
   */
</span>  var self = this;
  var http = require("http");
  var https = require("https");

  // Holds http.js objects
  var request;
  var response;

  // Request settings
  var settings = {};

  // Disable header blacklist.
  // Not part of XHR specs.
  var disableHeaderCheck = false;

  // Set some default headers
  var defaultHeaders = {
    "User-Agent": "node-XMLHttpRequest",
    "Accept": "*/*",
  };

  var headers = {};
  var headersCase = {};

  // These headers are not user setable.
  // The following are allowed but banned in the spec:
  // * user-agent
  var forbiddenRequestHeaders = [
    "accept-charset",
    "accept-encoding",
    "access-control-request-headers",
    "access-control-request-method",
    "connection",
    "content-length",
    "content-transfer-encoding",
    "cookie",
    "cookie2",
    "date",
    "expect",
    "host",
    "keep-alive",
    "origin",
    "referer",
    "te",
    "trailer",
    "transfer-encoding",
    "upgrade",
    "via"
  ];

  // These request methods are not allowed
  var forbiddenRequestMethods = [
    "TRACE",
    "TRACK",
    "CONNECT"
  ];

  // Send flag
  var sendFlag = false;
  // Error flag, used when errors occur or abort is called
  var errorFlag = false;

  // Event listeners
  var listeners = {};

  /**
   * Constants
   */

  this.UNSENT = 0;
  this.OPENED = 1;
  this.HEADERS_RECEIVED = 2;
  this.LOADING = 3;
  this.DONE = 4;

  /**
   * Public vars
   */

  // Current state
  this.readyState = this.UNSENT;

  // default ready state change handler in case one is not set or is set late
  this.onreadystatechange = null;

  // Result & response
  this.responseText = "";
  this.responseXML = "";
  this.status = null;
  this.statusText = null;

  // Whether cross-site Access-Control requests should be made using
  // credentials such as cookies or authorization headers
  this.withCredentials = false;

  /**
   * Private methods
   */

  /**
   * Check if the specified header is allowed.
   *
   * @param string header Header to validate
   * @return boolean False if not allowed, otherwise true
   */
  var isAllowedHttpHeader = function(header) {
    return disableHeaderCheck || (header && forbiddenRequestHeaders.indexOf(header.toLowerCase()) === -1);
  };

  /**
   * Check if the specified method is allowed.
   *
   * @param string method Request method to validate
   * @return boolean False if not allowed, otherwise true
   */
  var isAllowedHttpMethod = function(method) {
    return (method && forbiddenRequestMethods.indexOf(method) === -1);
  };

  /**
   * Public methods
   */

  /**
   * Open the connection. Currently supports local server requests.
   *
   * @param string method Connection method (eg GET, POST)
   * @param string url URL for the connection.
   * @param boolean async Asynchronous connection. Default is true.
   * @param string user Username for basic authentication (optional)
   * @param string password Password for basic authentication (optional)
   */
  this.open = function(method, url, async, user, password) {
    this.abort();
    errorFlag = false;

    // Check for valid request method
    if (!isAllowedHttpMethod(method)) {
      throw new Error("SecurityError: Request method not allowed");
    }

    settings = {
      "method": method,
      "url": url.toString(),
      "async": (typeof async !== "boolean" ? true : async),
      "user": user || null,
      "password": password || null
    };

    setState(this.OPENED);
  };

  /**
   * Disables or enables isAllowedHttpHeader() check the request. Enabled by default.
   * This does not conform to the W3C spec.
   *
   * @param boolean state Enable or disable header checking.
   */
  this.setDisableHeaderCheck = function(state) {
    disableHeaderCheck = state;
  };

  /**
   * Sets a header for the request or appends the value if one is already set.
   *
   * @param string header Header name
   * @param string value Header value
   */
  this.setRequestHeader = function(header, value) {
    if (this.readyState !== this.OPENED ...
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
