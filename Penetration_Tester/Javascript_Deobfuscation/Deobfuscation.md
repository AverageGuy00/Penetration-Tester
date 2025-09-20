# Beautify

We see that the current code we have is all written in a single line. This is known as `Minified JavaScript` code. In order to properly format the code, we need to `Beautify` our code. The most basic method for doing so is through our `Browser Dev Tools`.

```javascript
eval(function (p, a, c, k, e, d) { e = function (c) { return c.toString(36) }; if (!''.replace(/^/, String)) { while (c--) { d[c.toString(a)] = k[c] || c.toString(a) } k = [function (e) { return d[e] }]; e = function () { return '\\w+' }; c = 1 }; while (c--) { if (k[c]) { p = p.replace(new RegExp('\\b' + e(c) + '\\b', 'g'), k[c]) } } return p }('g 4(){0 5="6{7!}";0 1=8 a();0 2="/9.c";1.d("e",2,f);1.b(3)}', 17, 17, 'var|xhr|url|null|generateSerial|flag|HTB|flag|new|serial|XMLHttpRequest|send|php|open|POST|true|function'.split('|'), 0, {}))
```

```js
eval(
  (function (p, a, c, k, e, d) {
    e = function (c) {
      return c.toString(36);
    };
    if (!"".replace(/^/, String)) {
      while (c--) {
        d[c.toString(a)] = k[c] || c.toString(a);
      }
      k = [
        function (e) {
          return d[e];
        },
      ];
      e = function () {
        return "\\w+";
      };
      c = 1;
    }
    while (c--) {
      if (k[c]) {
        p = p.replace(new RegExp("\\b" + e(c) + "\\b", "g"), k[c]);
      }
    }
    return p;
  })(
    'g 4(){0 5="6{7!}";0 1=8 a();0 2="/9.c";1.d("e",2,f);1.b(3)}',
    17,
    17,
    "var|xhr|url|null|generateSerial|flag|HTB|flag|new|serial|XMLHttpRequest|send|php|open|POST|true|function".split(
      "|",
    ),
    0,
    {},
  ),
);
```


We can find many good online tools to deobfuscate JavaScript code and turn it into something we can understand. One good tool is [UnPacker](https://matthewfl.com/unPacker.html). Let's try copying our above-obfuscated code and run it in UnPacker by clicking the `UnPack` button.

```javascript
function generateSerial() {
  ...SNIP...
  var xhr = new XMLHttpRequest;
  var url = "/serial.php";
  xhr.open("POST", url, true);
  xhr.send(null);
};
```

#### Code Variables

The function starts by defining a variable `xhr`, which creates an object of `XMLHttpRequest`. As we may not know exactly what `XMLHttpRequest` does in JavaScript, let us Google `XMLHttpRequest` to see what it is used for.  
After we read about it, we see that it is a JavaScript function that handles web requests.

The second variable defined is the `URL` variable, which contains a URL to `/serial.php`, which should be on the same domain, as no domain was specified.

#### Code Functions

Next, we see that `xhr.open` is used with `"POST"` and `URL`. We can Google this function once again, and we see that it opens the HTTP request defined '`GET` or `POST`' to the `URL`, and then the next line `xhr.send` would send the request.

So, all `generateSerial` is doing is simply sending a `POST` request to `/serial.php`, without including any `POST` data or retrieving anything in return.

The developers may have implemented this function whenever they need to generate a serial, like when clicking on a certain `Generate Serial` button, for example. However, since we did not see any similar HTML elements that generate serials, the developers must not have used this function yet and kept it for future use.

With the use of code deobfuscation and code analysis, we were able to uncover this function. We can now attempt to replicate its functionality to see if it is handled on the server-side when sending a `POST` request. If the function is enabled and handled on the server-side, we may uncover an unreleased functionality, which usually tends to have bugs and vulnerabilities within them.

All we have to do is now send a `POST` request using `cURL` to `http://<IP>:<PORT>/serial.php` to get the flag. 