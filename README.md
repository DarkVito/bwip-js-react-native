# // Barcode Writer in Pure JavaScript


<a href="http://metafloor.github.io/bwip-js"><img alt="bwip-js" align="right" src="http://metafloor.github.io/bwip-js/images/bwip-js.png"></a>
bwip-js is a translation to native JavaScript of the amazing code provided in [Barcode Writer in Pure PostScript](https://github.com/bwipp/postscriptbarcode).  The translated code can run on any modern browser or JavaScript-based server framework.

The software has encoding modules for over 100 different barcode types and standards.
All linear and two-dimensional barcodes in common use (and many uncommon
ones) are available.  An exhaustive list of supported barcode types can be
found at the end of this document.  Barcode images are generated as png (node-js) or to a canvas (browser).

## Status 

* Current bwip-js version is 3.1.0 (2022-06-20)
* Current BWIPP version is 2022-06-10
* Node.js compatibility: 0.12+
* Browser compatibility: Edge, Firefox, Chrome

## Supported Platforms

* [Browser](#browser-usage)
* [React App](#react-usage)
* [React Native](#react-native-usage)
* [Node.js](#nodejs-request-handler)
* [Electron](#electron-example)
* [Command Line](#command-line-interface)

## Links

* [Home Page](http://metafloor.github.io/bwip-js/)
* [github Repository](https://github.com/metafloor/bwip-js)
* [`bwipjs` Methods Reference](https://github.com/metafloor/bwip-js/wiki/Methods-Reference)
* [Online Barcode Generator](http://metafloor.github.io/bwip-js/demo/demo.html)
* [Online Barcode API](https://github.com/metafloor/bwip-js/wiki/Online-Barcode-API)
* [npm Page](https://www.npmjs.com/package/bwip-js)
* [BWIPP Documentation](https://github.com/bwipp/postscriptbarcode/wiki)
* [Differences From BWIPP](https://github.com/metafloor/bwip-js/wiki/Differences-From-BWIPP)
* [Supported Barcode Types](https://github.com/metafloor/bwip-js/wiki/BWIPP-Barcode-Types)
* [Using Your Own Fonts](https://github.com/metafloor/bwip-js/wiki/Using-Your-Own-Fonts)
* [Annotated Example Drawing Object](https://github.com/metafloor/bwip-js/wiki/Annotated-Example-Drawing-Object)
* [Working with the Raw BWIPP Rendering Data](https://github.com/metafloor/bwip-js/wiki/Notes-on-the-Raw-BWIPP-Data)

## Installation

You can download the latest npm module using:

```
npm install bwip-js
```

Or the latest code from github:

    https://github.com/metafloor/bwip-js

(The bwip-js master branch and the npm version are kept sync'd.)

## Online Barcode Generator

An [online barcode generator](http://metafloor.github.io/bwip-js/demo/demo.html)
demonstrates all of the features of bwip-js.  The app is also available 
in the root bwip-js directory.  See [Demo Apps](#demo-apps).

## Online Barcode API

A bwip-js barcode service is available online, ready to serve up barcode images
on demand.

You can generate barcodes from anywhere on the web.  Embed the URLs in your
HTML documents or retrieve the barcode images directly from your non-JavaScript
server.  (JavaScript-based servers should use the bwip-js code directly - it will
be a lot more performant.)

For details on how to use this service, see [Online Barcode API](https://github.com/metafloor/bwip-js/wiki/Online-Barcode-API).

## Working With bwip-js Methods

Most of the public methods of the bwip-js export use an options object.  Only two values are required:

- `bcid` : The name of the barcode type/symbol.
- `text` : The text to encode.

All remaining options are optional, though you may find some quite useful.

The options values can be divided into two parts, bwip-js specific options and BWIPP options.

The bwip-js options are:

- `scaleX` : The x-axis scaling factor.  Must be an integer > 0.  Default is 2.
- `scaleY` : The y-axis scaling factor.  Must be an integer > 0.  Default is `scaleX`.
- `scale` : Sets both the x-axis and y-axis scaling factors.  Must be an integer > 0.

- `rotate` : Allows rotating the image to one of the four orthogonal orientations.  A string value.  Must be one of:

    - `'N'` : Normal (not rotated).  The default.
    - `'R'` : Clockwise (right) 90 degree rotation.
    - `'L'` : Counter-clockwise (left) 90 degree rotation.
    - `'I'` : Inverted 180 degree rotation.

- `padding` : Shorthand for setting `paddingtop`, `paddingleft`, `paddingright`, and `paddingbottom`.
- `paddingwidth` : Shorthand for setting `paddingleft` and `paddingright`.
- `paddingheight` : Shorthand for setting `paddingtop` and `paddingbottom`.
- `paddingtop` : Sets the height of the padding area, in points, on the top of the barcode image. Rotates and scales with the image.
- `paddingleft` : Sets the width of the padding area, in points, on the left side of the barcode image. Rotates and scales with the image.
- `paddingright` : Sets the width of the padding area, in points, on the right side of the barcode image. Rotates and scales with the image.
- `paddingbottom` : Sets the height of the padding area, in points, on the bottom of the barcode image. Rotates and scales with the image.
- `backgroundcolor` : This is actually a BWIPP option but is better handled by the bwip-js drawing code.  Takes either a hex RRGGBB or hex CCMMYYKK string value. 

For the BWIPP specific options, you will need to consult the
[BWIPP documentation](https://github.com/bwipp/postscriptbarcode/wiki)
to determine what options are available for each barcode type.

Note that bwip-js normalizes the BWIPP `width` and `height` options to always be in millimeters.
The resulting images are rendered at 72 dpi.  To convert to pixels, use a factor of 2.835 px/mm
(72 dpi / 25.4 mm/in).  The bwip-js scale options multiply the `width`, `height`, and `padding`.

>  An important note about the BWIPP `width` and `height` parameters.

Barcodes have the concept of module width (and height if a two-dimensional barcode).  For
linear barcodes, the module width is the width of the narrowest bar, and all other bar widths are
a multiple of it.  For 2D symbols, module width and height are the dimensions of the square
or rectangle that defines the symbol's layout grid.

For a barcode to be "in spec", the individual module dimensions must be consistent throughout the
symbol.  With high resolution printing, you can add/subtract a dot to adjust the size of individual
modules so the overall image meets the requested width or height, while still keeping the module
size within spec.  This is the intention behind BWIPP's `width` and `height` parameters.

bwip-js is designed for web usage, with a target display resolution of 72dpi.  (All of BWIPP's
internals are calculated in points and bwip-js just maps 1pt to 1px.)  At that low
resolution, it is not possible to add or subtract pixels without causing the symbol to go
out of spec.  Imagine a fairly common module width of 2px.  If you add or subtract a pixel,
you have changed the size by 50%.  Typical barcode specs require module sizes to be within 
5-10 pecent of nominal.

For this reason, bwip-js uses a constant module size so the resulting image is as 
large as possible, without exceeding the requested `width` or `height`.
The design causes the rendered barcodes to grow in "quantums".  An image will be
X-pixels wide with a module with of 2px, and Y-pixels wide with a module width of 3px,
and can not vary between those two sizes.

With bwip-js, the `scale` parameters can be thought of as requesting a particular module
width.  `scale=1` maps to a 1px module.  `scale=2` is a 2px module.  Etc.

When you specify `width`, you are effectively changing the scale of the final image.
Internally, bwip-js calcuates the requested `width x scale`, then divides
by the number of modules the symbol requires.  The floor of that value is the
module width (scale) of the rendered barcode.


<a name="browser-usage"></a>
## Browser Usage

To use within a browser, add the following to the head of your page:

```
<script type="text/javascript" src="file-or-url-path-to/bwip-js/dist/bwip-js-min.js"></script>
```

While developing your project, you may want to use `dist/bwip-js.js` to get better stack
traces.

If you are using `RequireJS` or a common-js bundling utility, the bwip-js scripts are
structured as UMDs and should work with your environment.

The scripts adds a single `bwipjs` global object.  To draw a barcode to a canvas:

```javascript
try {
    // The return value is the canvas element
    let canvas = bwipjs.toCanvas('mycanvas', {
            bcid:        'code128',       // Barcode type
            text:        '0123456789',    // Text to encode
            scale:       3,               // 3x scaling factor
            height:      10,              // Bar height, in millimeters
            includetext: true,            // Show human-readable text
            textxalign:  'center',        // Always good to set this
        });
} catch (e) {
    // `e` may be a string or Error object
}
```

The `bwipjs.toCanvas()` method takes two parameters:

* The canvas on which to render the barcode.  This can by an `id` string or the actual
  canvas element.  The rendering will automatically resize the canvas to match the
  barcode image.
* A bwip-js/BWIPP options object.
 
On return from `toCanvas()`, the barcode image will have been fully rendered to the canvas.

If you would prefer to display the barcode using an `<img>` tag or with CSS `background-image`,
pass in a detached or hidden canvas, and use the canvas method
[HTMLCanvasElement.toDataURL](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement/toDataURL)
to get a data URL. For example:

```javascript
let canvas = document.createElement('canvas');
try {
    bwipjs.toCanvas(canvas, options);
    document.getElementById('my-img').src = canvas.toDataURL('image/png');
} catch (e) {
    // `e` may be a string or Error object
}
```

<a name="browser-es6-module-usage"></a>
## Browser ES6 Module Usage

The ESM provides the same API as the standard browser module using:

```javascript
import bwipjs from 'bwip-js';

// ... identical bwipjs.toCanvas() interface as above ...
```

The ESM also facilitates bundler tree shaking by providing the individual encoders as named exports.
Each exported encoder functions identically to `bwipjs.toCanvas()`.

The exported names are the same as the `bcid` names, with the caveat that dashes `-` are replaced with
underscores `_`.  For example, to import the `gs1-128` encoder, you would use:

```javascript
import { gs1_128 } from 'bwip-js';

try {
    gs1_128('my-canvas', options);
} catch (e) {
    // `e` may be a string or Error object
}
```

<a name="react-usage"></a>
## React Usage

The following is a minimal example of bwip-js in a React app.
It is based on the default `App.js` file generated by `create-react-app`.

```javascript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import bwipjs from 'bwip-js';

class App extends Component {
  componentDidMount() {
    try {
      // The return value is the canvas element
      let canvas = bwipjs.toCanvas('mycanvas', {
                bcid:        'code128',       // Barcode type
                text:        '0123456789',    // Text to encode
                scale:       3,               // 3x scaling factor
                height:      10,              // Bar height, in millimeters
                includetext: true,            // Show human-readable text
                textxalign:  'center',        // Always good to set this
            });
    } catch (e) {
        // `e` may be a string or Error object
    }
  }
  render() {
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>Welcome to React</h2>
        </div>
        <canvas id="mycanvas"></canvas>
      </div>
    );
  }
}
export default App;
```

See the Browser Usage section for details on the `toCanvas()` method. 

See the ES6 Browser Module Usage section for details on importing encoders directly.

<a name="react-native-usage"></a>
## React Native usage
```javascript
import React from "react"
import { SvgXml } from "react-native-svg";
import bwipjs from 'bwip-js'
import DrawingSVG from 'bwip-js/examples/drawing-svg.js'

export const BarCode = (options) => {
    let xml = null;
    try {
        bwipjs.fixupOptions(options);
        xml = bwipjs.render(options, DrawingSVG(options, bwipjs.FontLib));
    } catch (e) {

    }
    return (
        <SvgXml xml={xml} />
    )
}
```
<a name="nodejs-request-handler"></a>
## Node.js Request Handler

The online barcode API is implemented as a Node.js application.
See the [Online Barcode API](https://github.com/metafloor/bwip-js/wiki/Online-Barcode-API) for details on how the URL query parameters must be structured.

A working, minimal example of how to use the request handler can be found in
`server.js`:

```javascript
// Simple HTTP server that renders barcode images using bwip-js.
const http   = require('http');
const bwipjs = require('bwip-js');

// This shows how to load the Inconsolata font, supplied with the bwip-js distribution.
// The path to your fonts will be different.
//bwipjs.loadFont('Inconsolata', 100,
//      require('fs').readFileSync('./fonts/Inconsolata.otf', 'binary'));

http.createServer(function(req, res) {
    // If the url does not begin /?bcid= then 404.  Otherwise, we end up
    // returning 400 on requests like favicon.ico.
    if (req.url.indexOf('/?bcid=') != 0) {
        res.writeHead(404, { 'Content-Type':'text/plain' });
        res.end('BWIPJS: Unknown request format.', 'utf8');
    } else {
        bwipjs.request(req, res); // Executes asynchronously
    }

}).listen(3030);
```

If you run the above code on your local machine, you can test with the following URL:

```
http://localhost:3030/?bcid=isbn&text=978-1-56581-231-4+52250&includetext&guardwhitespace
```

The bwip-js request handler only operates on the URL query parameters and
ignores all path information.  Your application is free to structure the URL
path as needed to implement the desired HTTP request routing.

<a name="nodejs-image-generator"></a>
## Node.js Image Generator

You can use bwip-js to generate PNG images directly.

```javascript
const bwipjs = require('bwip-js');

bwipjs.toBuffer({
        bcid:        'code128',       // Barcode type
        text:        '0123456789',    // Text to encode
        scale:       3,               // 3x scaling factor
        height:      10,              // Bar height, in millimeters
        includetext: true,            // Show human-readable text
        textxalign:  'center',        // Always good to set this
    }, function (err, png) {
        if (err) {
            // `err` may be a string or Error object
        } else {
            // `png` is a Buffer
            // png.length           : PNG file length
            // png.readUInt32BE(16) : PNG image width
            // png.readUInt32BE(20) : PNG image height
        }
    });
```

If you would prefer to work with Promises, omit the callback function and
`toBuffer()` will return a Promise:

```javascript
bwipjs.toBuffer({
        bcid:        'code128',       // Barcode type
        text:        '0123456789',    // Text to encode
        scale:       3,               // 3x scaling factor
        height:      10,              // Bar height, in millimeters
        includetext: true,            // Show human-readable text
        textxalign:  'center',        // Always good to set this
    })
    .then(png => {
        // `png` is a Buffer as in the example above
    })
    .catch(err => {
        // `err` may be a string or Error object
    });
```

<a name="nodejs-es6-module-usage"></a>
## Node.js ES6 Module Usage

The ESM provides the same API as `require('bwip-js')` using:

```javascript
import bwipjs from 'bwip-js';

// ... identical to the examples above ...
```

The ESM also facilitates bundler tree-shaking by providing the individual encoders as named exports.  Each exported encoder functions identically to `bwipjs.toBuffer()`.

The exported names are the same as the `bcid` names, with the caveat that dashes `-` are replaced with underscores `_`.  For example, to import the `gs1-128` encoder, you would use:

```javascript
import { gs1_128 } from 'bwip-js';

try {
    let buf = await gs1_128(options);
} catch (e) {
    // `e` may be a string or Error object
}
```

When named encoders are imported, the `bcid` value in the options object is ignored.

<a name="electron-example"></a>
## Electron Example

There have been some changes to the Electron bundler, and it may pull in either the
nodejs or browser module, depending on your version of Electron.  The example below
assumes the nodejs module.

If you try this example and get the error `bwipjs.toBuffer is not a function`, the
Electron bundler grabbed the browser module.  See the [Browser Usage](#browser-usage)
section above and draw to a canvas instead.

This is an example `index.html` file for a basic, single window app:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
  </head>
  <body>
    Node.js <script>document.write(process.versions.node)</script>,
    Chromium <script>document.write(process.versions.chrome)</script>,
    Electron <script>document.write(process.versions.electron)</script>,
    bwip-js <script>document.write(bwipjs.VERSION)</script>,
    and BWIPP <script>document.write(bwipjs.BWIPP.VERSION)</script>.
    <br><br><img id="myimg">
    <pre id="output"></pre>
  </body>

  <script>
    var bwipjs = require('bwip-js');
    bwipjs.toBuffer({ bcid:'qrcode', text:'0123456789' }, function (err, png) {
        if (err) {
          document.getElementById('output').textContent = err;
        } else {
          document.getElementById('myimg').src = 'data:image/png;base64,' +
                                                 png.toString('base64');
        }
      });
  </script>
</html>
```

<a name="command-line-interface"></a>
## Command Line Interface

bwip-js can be used as a command line tool when installed globally:

```
$ npm install -g bwip-js
$ bwip-js
Usage: bwip-js symbol-name text [options...] file-name
       bwip-js --bcid=symbol-name --text=text [options...] file-name

Example:
       bwip-js code128 012345678 includetext textcolor=ff0000 my-code128.png

file-name must be type png.

Try 'bwip-js --help' for more information.
Try 'bwip-js --symbols' for a list of supported barcode symbols.
```

To use a custom font with the command line utility, use the `--loadfont` option.  It
takes one of three formats:

`--loadfont=`_font-name_`,`_y-mult_`,`_x-mult_`,`_path-to-font-file_ <br>
`--loadfont=`_font-name_`,`_size-mult_`,`_path-to-font-file_ <br>
`--loadfont=`_font-name_`,`_path-to-font-file_ <br>

For example:

```
$ bwip-js code128 12345678 includetext textfont=CONS textxalign=center \
  loadfont=CONS,250,100,../fonts/Inconsolata.otf /tmp/code128.png
```

The above demonstrates how to maniplulate the font metrics so the characters appear
tall and narrow.


## Demo HTML App

`demo.html` located in the root bwip-js directory is a full featured demonstation
of bwip-js barcode rendering.  It uses bwip-js' built-in graphics to draw to a canvas.
The images produced will match pixel-for-pixel with the images produced by the same
nodejs usage.


## Examples

There are example html and node apps provided with the project including
how to write your own drawing interface, generating SVG barcode images, 
and adding scalable barcodes to a [pdfkit](https://pdfkit.org/) document.

See the examples [README](https://github.com/metafloor/bwip-js/tree/master/examples) 
for more details.

