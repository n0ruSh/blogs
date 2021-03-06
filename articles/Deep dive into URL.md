> The **`URL`** interface is used to parse, construct, normalize, and encode [URLs](https://developer.mozilla.org/en-US/docs/Glossary/URL). It works by providing properties which allow you to easily read and modify the components of a URL

## Basic

### [SearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URL/searchParams)

> The **`searchParams`** readonly property of the [`URL`](https://developer.mozilla.org/en-US/docs/Web/API/URL) interface returns a [`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) object allowing access to the [`GET`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET) decoded query arguments contained in the URL.

```javascript
const url = new URL('https://www.google.com:443/index.html?a=1&b=2#hash');
/**
 *	URL {
 *		href: "https://www.google.com:443/index.html?a=1&b=2#hash",
 *		protocol: "https:",
 *		username: "",
 *		password: "",
 *		hash: "#hash,
 *		origin: "https://www.google.com:443", // with port, with protocl
 *		host: "www.google.com:443", // with port, without protocol
 *		hostname: "www.google.com", // without port, without protocl
 *		search: "?a=1&b=2"
 *		searchParams: {} // URLSeachParams
 *	}
*/
url.searchParams.get('a'); // '1'
```
## createObjectURL

> The **`URL.createObjectURL()`** static method creates a [`DOMString`](https://developer.mozilla.org/en-US/docs/Web/API/DOMString) containing a URL representing the object given in the parameter. The URL lifetime is tied to the [`document`](https://developer.mozilla.org/en-US/docs/Web/API/Document) in the window on which it was created. The new object URL represents the specified [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File) object or [`Blob`](https://developer.mozilla.org/en-US/docs/Web/API/Blob) object.

### Blob

> The **`Blob`** object represents a blob, which is a file-like object of immutable, raw data; they can be read as text or binary data, or converted into a [`ReadableStream`](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) so its methods can be used for processing the data.

```javascript
const obj = {hello: 'world'};
const blob = new Blob([JSON.stringify(obj, null, 2)], {type : 'application/json'});
/**
 *	Blob {
 *		size: 22, // JSON.stringify(obj, null, 2).length === 22
 *		type: 'application/json'
 *	}
 */
// get usable url: "blob:https://developer.mozilla.org/ff415f41-2b1b-4e47-bc1c-37112e0ff4b2"
const linkUrl = URL.createObjectURL(blob);
```
### File

> A `File` object is a specific kind of a [`Blob`](https://developer.mozilla.org/en-US/docs/Web/API/Blob), and can be used in any context that a Blob can. In particular, [`FileReader`](https://developer.mozilla.org/en-US/docs/Web/API/FileReader), [`URL.createObjectURL()`](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL), [`createImageBitmap()`](https://developer.mozilla.org/en-US/docs/Web/API/ImageBitmapFactories/createImageBitmap), and [`XMLHttpRequest.send()`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest#send()) accept both `Blob`s and `File`s

```javascript
const file = new File(["hello", "world"], "test.txt", {
  type: "text/plain",
});

/**
 * File { 
 * 	name: "test.txt",
 *	lastModified: 1594093337229,
 *	lastModifiedDate: Tue Jul 07 2020 11:42:17 GMT+0800 (China Standard Time), 
 *	webkitRelativePath: "",
 *	size: 10,
 *	type: "text/plain",
 *	__proto__: File
 *}
 */
```
#### Preveiw uploaded images
```javascript
export default function App() {
  function onChange(e) {
    const file = e.target.files[0]; // get file object
    const div = document.getElementById('img');
    const img = document.createElement("img");
    const url = URL.createObjectURL(file); // convert to url
    img.src = url;
    div.appendChild(img);
  }

  return (
    <div>
      <input type="file" id="input" onChange={onChange} />
      <div id='img'>
      </div>
    </div>
  );
}
```
[demo](https://codesandbox.io/s/preview-file-330tx?file=/src/App.js)

### FileReader

> The **`FileReader`** object lets web applications asynchronously read the contents of files (or raw data buffers) stored on the user's computer, using [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File) or [`Blob`](https://developer.mozilla.org/en-US/docs/Web/API/Blob) objects to specify the file or data to read

```javascript
export default function App() {
  function onChange(e) {
    const file = e.target.files[0];
    const div = document.getElementById("img");
    const img = document.createElement("img");
    const reader = new FileReader();
    reader.onload = function(e) {
      img.src = e.target.result; // base64 data of the uploaded image
      div.appendChild(img);
    };
    reader.readAsDataURL(file);
  }

  return (
    <div>
      <input type="file" id="input" onChange={onChange} />
      <div id="img" />
    </div>
  );
}

```
[demo](https://codesandbox.io/s/base64-22zjd?file=/src/App.js:414-446)

## Real life case

I get an [error](https://github.com/mozilla/pdf.js/issues/7612) reporting `Setting up fake worker` when using [pdfjs-dist](https://www.npmjs.com/package/pdfjs-dist).  The users are supposed to set up worker as following:

```javascript
import pdflib from 'pdfjs-dist';
/** 
 * set up with the address pointing to the worker file
 * the address is a String and not dealt by bunlding tool like webpack
 * the relative path doesnt work for the bundle after packaging
 */
pdflib.PDFJS.workerSrc = './node_modules/pdfjs-dist/build/pdf.worker.entry.js';
```
### solution
```javascript
import pdfjsLib from 'pdfjs-dist';
import pdfjsWorker from 'raw-loader!pdfjs-dist/build/pdf.worker.min';

const pdfjsWorkerBlob = new Blob([pdfjsWorker]);
const pdfjsWorkerBlobURL = URL.createObjectURL(pdfjsWorkerBlob);

pdfjsLib.GlobalWorkerOptions.workerSrc = pdfjsWorkerBlobURL;
```
