# SWE432 Hands On Sessions
**last update: Fall 2021**

During the semester this repo will be updated on Thursdays before the TA office hours, the goal is to further explore concepts learned in the lectures and work closely with their related code.

## Session 1: Vanilla JS vs. French Vanilla JS (1990s vs. 2020s) - September 7th
JavaScript (JS) is the standard language for web browsers and becoming popular among web servers.
It has come a long way, isn't it? In a nutshell:

### JS is Loosely typed 
Like Python, Perl, or PHP, so no need for ```String x= "";```, instead ```let x="";``` does the trick.

Yet you can be explicit when manipulating data types: 
- String casting:
```js
let x = '1', y =1; let z= x+y; // string concat 
```
- Integer casting:
```js
let x = '1', y =1; let z= +x+y; // int sum
```
### JS uses JavaScript Object Notation (JSON)
To manipulate data, you can count on the following syntax:

- Primitives:
```js
let x = '1', y =1, z = true, w = null; 
```

- Objects and Arrays:
```js
let z= {x: '1', y:1}, w = ['1', 1];
```

### ES2021 (wait what!?)
ECMAScript (ES) 2021 is the current general reference specification to build the Javascript engines we use in our web apps. For instance, Google built the V8 JS engine and updated recently to satisfy ES2021.

JS is often used with other frameworks and libraries making it hard to differentiate which features are native and which are from third-parties, so when refering to JS directly, the community refers to it as *Vanilla JS*. To further differentiate its original flavor from all the features that have been added to it through the years, I call its current version, *French Vanilla JS*.

Since developers and content online may not be up to date with JS evolution, this is a brief summary of features that has evolved recently, achieving the same functionality with a more succint syntax:

#### From Functions to Arrow Functions 
```js
// Functions
function z (x, y) { return x+y; }
z(1, 2); 
```

```js
// Arrow Functions 
const z = (x, y) => x+y;
z(1, 2); 
```
#### From Object construction to Object spreading 
```js
// Object construction
let z= {x: '1', y:1}, a = true; 
let w = {x: z.x, y: z.y, a: a};
w;
```
```js
// Object spreading 
let z= {x: '1', y:1}, a = true; 
let w = {...z, a};
w;
```

#### From Object property extraction to Object destructuring
```js
// Object property extraction
let z= {x: '1', y:1}; 
z.x + z.y;
```
```js
// Object destructuring
let z= {x: '1', y:1}; let {x, y} = z;
x + y;
```

#### From Explicit data iterators to Implicit data iterators
```js
// Explicit data iterators
let z= ['1', 1];
for(let i = 0; i < z.length; i++)
{ let item = z[i];}
```
```js
// Implicit data iterators 
let z= ['1', 1];
for(let i in z){let item = z[i];}
for(let item of z){}
```

#### From Conditional Expressions to Conditional Chainin
```js
// Conditional Expressions 
let x= ()=>true;
 x ? x(): null;
```
```js
// Conditional Chaining
let x= ()=>true;
 x?.();
```

#### From Null Checks to Nullish coalescing
```js
// Null Checks
let x = ’’; 
let y = x!== null && x!== undefined? x:'';
```
```js
// Nullish coalescing
let x = ''; let y = x ??'value’;
```

## Session 2: Installing and Running NPM Locally	- Sept 2nd
### Go to your browser

1. Install [NPM](https://nodejs.org/), it comes bundled with Node.js.

### Go to a terminal in your machine

2. Locate the folder containing the `package.json` file and run these commands:
```ShellSession
npm install
npm run start
```

In the case of `npm install`, you only have to run it when you add packages to your `package.json`.

### Troubleshooting
If you get a cryptic error like `throw er; // Unhandled 'error' event` during `npm install`, try removing the package-lock.json file and the node_modules folder, then try re-running the command.

Alternatively, open a terminal, locate your project's root folder, and run these commands:

#### Unik-Like Systems
```ShellSession
rm package-lock.json
rm yarn.lock
rm -rf ./node_modules
npm install
```

#### Windows (PowerShell)
```ShellSession
rm package-lock.json
rm yarn.lock
rd -r .\node_modules
npm install
```
## Session 3: From XHR to Promises	- September 7th

## Session 4: From Fetch to Axios	- September 14th	

Try using this API to make the same server request with 4 different libraries. You can run it [live](https://codesandbox.io/s/swe-432-react-popcorn-sales-forked-cieih).

This is an API usage example:
```js
// function getPlaceAjax
//  parameter: zip
//  parameter: success callback
//  parameter: error callback (optional)
//  parameter: url (optional)
//  library: string, one of: "promise" (default), "ajax", "axios", "fetch" (optional)
//  action: create the XMLHttpRequest object, register the
//          handler for onreadystatechange, prepare to send
//          the request (with open), and send the request,
//          along with the zip code, to the server
//  includes: the anonymous handler for onreadystatechange
 getPlace('22033', (data)=>{console.log(data)}, error=>console.warn(error), "https://swe432tomcat.herokuapp.com/zipLookup", "fetch");
```

And this is the API implementation:
```js
import axios from "axios";
// popcornA.js
//  Ajax JavaScript code for the popcornA.html document
//  Thanks to Sebesta, Programming the World Wide Web

/********************************************************/

export function getPlaceAjax(
  zip,
  thenCallback,
  catchCallback,
  url = "https://swe432tomcat.herokuapp.com/zipLookup"
) {
  let xhr = null;
  if (window.XMLHttpRequest) {
    // IE7+, Firefox, Chrome, Opera, Safari
    xhr = new XMLHttpRequest();
  } else {
    // IE5, IE6
    // eslint-disable-next-line
    xhr = new ActiveXObject("Microsoft.XMLHTTP");
  }

  // Register the embedded handler function
  // This function will be called when the server returns
  // (the "callback" function)
  xhr.onreadystatechange = () => {
    // 4 means finished,
    if (xhr.readyState === 4) {
      // and 200 means okay.
      if (xhr.status === 200) {
        // Data should look like "Fairfax, Virginia"
        thenCallback(xhr.responseText);
      } else {
        catchCallback(xhr.statusText, xhr);
      }
    }
  };
  // Call the response software component
  const query = url + "?zip=" + zip;
  xhr.open("GET", query);
  xhr.send(null);
}

export function getPlaceAjaxPromise(
  zip,
  thenCallback,
  catchCallback,
  url = "https://swe432tomcat.herokuapp.com/zipLookup"
) {
  const zipPromise = new Promise((resolutionFunc, rejectionFunc) => {
    let xhr = null;
    if (window.XMLHttpRequest) {
      // IE7+, Firefox, Chrome, Opera, Safari
      xhr = new XMLHttpRequest();
    } else {
      // IE5, IE6
      // eslint-disable-next-line
      xhr = new ActiveXObject("Microsoft.XMLHTTP");
    }

    // Register the embedded handler function
    // This function will be called when the server returns
    // (the "callback" function)
    xhr.onreadystatechange = () => {
      // 4 means finished,
      if (xhr.readyState === 4) {
        // and 200 means okay.
        if (xhr.status === 200) {
          // Data should look like "Fairfax, Virginia"
          resolutionFunc(xhr.responseText);
        } else {
          rejectionFunc(xhr.statusText, xhr);
        }
      }
    };
    // Call the response software component
    const query = url + "?zip=" + zip;
    xhr.open("GET", query);
    xhr.send(null);
  });

  zipPromise.then(thenCallback).catch(catchCallback);
}

export function getPlaceFetch(
  zip,
  thenCallback,
  catchCallback,
  url = "https://swe432tomcat.herokuapp.com/zipLookup"
) {
  // Call the response software component
  const query = url + "?zip=" + zip;
  fetch(query)
    .then(
      // Register the embedded handler function
      // This function will be called when the server returns
      // (the "callback" function)
      // 4 means finished, and 200 means okay.
      (response) => {
        // Data should look like "Fairfax, Virginia"
        response.text().then(thenCallback);
      }
    )
    .catch(catchCallback);
}

export function getPlaceAxios(
  zip,
  thenCallback,
  catchCallback,
  url = "https://swe432tomcat.herokuapp.com/zipLookup"
) {
  // Call the response software component
  const query = "zip=" + zip;
  axios
    .post(url, query)
    .then(
      // Register the embedded handler function
      // This function will be called when the server returns
      // (the "callback" function)
      // 4 means finished, and 200 means okay.
      (response) => {
        // Data should look like "Fairfax, Virginia"
        thenCallback(response.data);
      }
    )
    .catch(catchCallback);
}

function getPlace(
  zip,
  thenCallback,
  catchCallback,
  url = "https://swe432tomcat.herokuapp.com/zipLookup",
  library = "promise"
) {
  let targetLibrary = null;
  switch (library) {
    case "axios":
      targetLibrary = getPlaceAxios;
      break;
    case "fetch":
      targetLibrary = getPlaceFetch;
      break;
    case "promise":
      targetLibrary = getPlaceAjaxPromise;
      break;
    case "ajax":
    default:
      targetLibrary = getPlaceAjax;
  }
  targetLibrary(zip, thenCallback, catchCallback, url);
}

export default getPlace;

```
# Coming soon
## Session 5: A Chat on Fire!	- September 21st	
## Session 6: Servlet Microservices	- September 28th	
## Session 7: Reacting to Client Requests using Microservices	- October 19th	
## Session 8: Material UI -	October 26th


