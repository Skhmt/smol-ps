# smol-ps

small pubsub, mostly for fun code golfing

provided only as code snippets in this `readme` because they're just a single line and honestly probably should not be used in production anyway

## pollutes global/window ns, minified 97 bytes:

```js
let t={};sub=(e,c)=>((t[e]??=new Set).add(c),_=>t[e].delete(c));pub=(e,d)=>t[e]?.forEach(f=>f(d))
```

you could put it into a module and then call `sub(event, callback)` and `pub(event, data)` from the global/window namespace

you could of course save 4 additional bytes by changing `sub` to `s` and `pub` to `p`, but the convention is to use those names

expanded and explained:

```js
let dictionary = {};

var sub = (event, callback) => { // note the `var` here, which is implicit in the minified version, making it a global variable
  // `(t[e]??=new Set)` combines nullish coalescing assignment and removes the optional () after a new object with no arguments
  if (dictionary[event] === null) dictionary[event] = new Set();
  dictionary[event].add(callback);

  // comma syntax evaluates the first line then evaluates and returns the second (or last) line
  // this saves some bytes from the word `return` to take advantage of implicit returns if not using braces.
  return () => dictionary[event].delete(callback);
};

var pub = (event, data) => {
  if (dictionary[event]) {
    dictionary[event].forEach(callback => callback(data))
  }
};
```

## exportable, minified 104 bytes:

```js
export let d={},sub=(e,c)=>((d[e]??=new Set).add(c),_=>d[e].delete(c)),pub=(e,s)=>d[e]?.forEach(f=>f(s))
```

you would put it into a module then `import {sub, pub} from 'ps.js'`

you would *not* import `d`, it's just being exported to save bytes by reusing the existing `let` declaration.

### others:

- 149 bytes: https://github.com/hassanshaikley/pico-pubsub
- <500 bytes: https://github.com/bjoerge/nano-pubsub
- 827 bytes: https://github.com/LukeWood/tiny-pubsub
