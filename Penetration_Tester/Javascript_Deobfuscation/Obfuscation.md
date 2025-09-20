Obfuscation is a technique used to make a script more difficult to read by humans but allows it to function the same from a technical point of view, though performance may be slower. This is usually achieved automatically by using an obfuscation tool, which takes code as an input, and attempts to re-write the code in a way that is much more difficult to read, depending on its design.


---
# Basic obfuscation

Example = `console.log('HTB Javascript Deobfuscation Module')`

Result: 

```js
eval(function(p,a,c,k,e,d){e=function(c){return c};if(!''.replace(/^/,String)){while(c--){d[c]=k[c]||c}k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1};while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c])}}return p}('1.0(\'2 3 4 5\')',6,6,'log|console|HTB|Javascript|Deobfuscation|Module'.split('|'),0,{}))
```

A `packer` obfuscation tool usually attempts to convert all words and symbols of the code into a list or a dictionary and then refer to them using the `(p,a,c,k,e,d)` function to re-build the original code during execution. The `(p,a,c,k,e,d)` can be different from one packer to another. However, it usually contains a certain order in which the words and symbols of the original code were packed to know how to order them during execution.

---

# Advanced Obfuscation

On `https://obfuscator.io`, we will chnage the `String Array Encoding` to `Base64`.

Example = `console.log('HTB Javascript Deobfuscation Module')`

```js
function _0x4138e9(_0x42030f,_0xc0fb89){return _0x1c74(_0x42030f-0x2dc,_0xc0fb89);}function _0x1811(){var _0x20f821=['sfrciePHDMfZy3jPChqGrgvVyMz1C2nHDgLVBIbnB2r1Bgu','mJq0mdHhz3rSEu8','mZGYntiYBfn5s2ne','Bg9N','mJeXmdyZmg9dtLvsyW','muXLzKrjuq','m1HxAwD5uG','mty2ntaZnNzWzgHhsW','nvrUCKrjtq','mJjqsxL1tLq','mJm4mdeWnffzEu5tqW','mtuZnJG0nNbtzezXqW','ntiYmte2A2jqsxP5'];_0x1811=function(){return _0x20f821;};return _0x1811();}(function(_0x155e7b,_0x1d961a){var _0x56268b=_0x155e7b();function _0x2263b0(_0x7459a1,_0xe25fa9){return _0x1c74(_0x7459a1-0x24a,_0xe25fa9);}while(!![]){try{var _0x366ac3=parseInt(_0x2263b0(0x2e2,0x2e2))/0x1*(parseInt(_0x2263b0(0x2e9,0x2e6))/0x2)+parseInt(_0x2263b0(0x2e3,0x2e4))/0x3*(parseInt(_0x2263b0(0x2e4,0x2e5))/0x4)+parseInt(_0x2263b0(0x2e5,0x2e3))/0x5*(-parseInt(_0x2263b0(0x2e8,0x2e4))/0x6)+-parseInt(_0x2263b0(0x2df,0x2df))/0x7+parseInt(_0x2263b0(0x2e7,0x2e5))/0x8+-parseInt(_0x2263b0(0x2de,0x2de))/0x9+parseInt(_0x2263b0(0x2e1,0x2de))/0xa*(-parseInt(_0x2263b0(0x2e6,0x2e7))/0xb);if(_0x366ac3===_0x1d961a){break;}else{_0x56268b['push'](_0x56268b['shift']());}}catch(_0x5918c5){_0x56268b['push'](_0x56268b['shift']());}}}(_0x1811,0x3a665));function _0x1c74(_0x3043df,_0x150a7d){var _0x181178=_0x1811();_0x1c74=function(_0x1c7486,_0x51a0f5){_0x1c7486=_0x1c7486-0x93;var _0x15fd4c=_0x181178[_0x1c7486];if(_0x1c74['VgdhAJ']===undefined){var _0x2d6a2e=function(_0xec23dc){var _0x49b4b0='abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+/=';var _0x592c5e='';var _0x162943='';for(var _0x2a3e0d=0x0,_0x4d56c4,_0x2f6b12,_0x58fcff=0x0;_0x2f6b12=_0xec23dc['charAt'](_0x58fcff++);~_0x2f6b12&&(_0x4d56c4=_0x2a3e0d%0x4?_0x4d56c4*0x40+_0x2f6b12:_0x2f6b12,_0x2a3e0d++%0x4)?_0x592c5e+=String['fromCharCode'](0xff&_0x4d56c4>>(-0x2*_0x2a3e0d&0x6)):0x0){_0x2f6b12=_0x49b4b0['indexOf'](_0x2f6b12);}for(var _0x31d767=0x0,_0x59c9e2=_0x592c5e['length'];_0x31d767<_0x59c9e2;_0x31d767++){_0x162943+='%'+('00'+_0x592c5e['charCodeAt'](_0x31d767)['toString'](0x10))['slice'](-0x2);}return decodeURIComponent(_0x162943);};_0x1c74['BMpipX']=_0x2d6a2e;_0x3043df=arguments;_0x1c74['VgdhAJ']=!![];}var _0x58728b=_0x181178[0x0];var _0x5a5def=_0x1c7486+_0x58728b;var _0x5d23ba=_0x3043df[_0x5a5def];if(!_0x5d23ba){_0x15fd4c=_0x1c74['BMpipX'](_0x15fd4c);_0x3043df[_0x5a5def]=_0x15fd4c;}else{_0x15fd4c=_0x5d23ba;}return _0x15fd4c;};return _0x1c74(_0x3043df,_0x150a7d);}console[_0x4138e9(0x372,0x378)](_0x4138e9(0x36f,0x36d));
```

This code is obviously more obfuscated, and we can't see any remnants of our original code.

---

