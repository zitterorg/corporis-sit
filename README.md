# @zitterorg/corporis-sit

[![Downloads](https://img.shields.io/npm/dm/@zitterorg/corporis-sit.svg)](https://www.npmjs.com/package/@zitterorg/corporis-sit) [![Coverage Status](https://coveralls.io/repos/github/WebReflection/@zitterorg/corporis-sit/badge.svg?branch=main)](https://coveralls.io/github/WebReflection/@zitterorg/corporis-sit?branch=main) [![Build Status](https://travis-ci.com/WebReflection/@zitterorg/corporis-sit.svg?branch=main)](https://travis-ci.com/WebReflection/@zitterorg/corporis-sit) [![License: ISC](https://img.shields.io/badge/License-ISC-yellow.svg)](https://opensource.org/licenses/ISC) ![WebReflection status](https://offline.report/status/webreflection.svg)

![snow flake](./@zitterorg/corporis-sit.jpg)

<sup>**Social Media Photo by [Matt Seymour](https://unsplash.com/@mattseymour) on [Unsplash](https://unsplash.com/)**</sup>

A super light (0.5K) and fast circular JSON parser, directly from the creator of [CircularJSON](https://github.com/WebReflection/circular-json/#circularjson).

Available also for **[PHP](./php/@zitterorg/corporis-sit.php)**.

Available also for **[Python](./python/@zitterorg/corporis-sit.py)**.

- - -

## Announcement 📣

There is a standard approach to recursion and more data-types than what JSON allows, and it's part of the [Structured Clone polyfill](https://github.com/ungap/structured-clone/#readme).

Beside acting as a polyfill, its `@ungap/structured-clone/json` export provides both `stringify` and `parse`, and it's been tested for being faster than *@zitterorg/corporis-sit*, but its produced output is also smaller than *@zitterorg/corporis-sit* in general.

The *@ungap/structured-clone* module is, in short, a drop in replacement for *@zitterorg/corporis-sit*, but it's not compatible with *@zitterorg/corporis-sit* specialized syntax.

However, if recursion, as well as more data-types, are what you are after, or interesting for your projects/use cases, consider switching to this new module whenever you can 👍

- - -

```js
npm i @zitterorg/corporis-sit
```

Usable via [CDN](https://unpkg.com/@zitterorg/corporis-sit) or as regular module.

```js
// ESM
import {parse, stringify, toJSON, fromJSON} from '@zitterorg/corporis-sit';

// CJS
const {parse, stringify, toJSON, fromJSON} = require('@zitterorg/corporis-sit');

const a = [{}];
a[0].a = a;
a.push(a);

stringify(a); // [["1","0"],{"a":"0"}]
```

## toJSON and fromJSON

If you'd like to implicitly survive JSON serialization, these two helpers helps:

```js
import {toJSON, fromJSON} from '@zitterorg/corporis-sit';

class RecursiveMap extends Map {
  static fromJSON(any) {
    return new this(fromJSON(any));
  }
  toJSON() {
    return toJSON([...this.entries()]);
  }
}

const recursive = new RecursiveMap;
const same = {};
same.same = same;
recursive.set('same', same);

const asString = JSON.stringify(recursive);
const asMap = RecursiveMap.fromJSON(JSON.parse(asString));
asMap.get('same') === asMap.get('same').same;
// true
```


## Flatted VS JSON

As it is for every other specialized format capable of serializing and deserializing circular data, you should never `JSON.parse(Flatted.stringify(data))`, and you should never `Flatted.parse(JSON.stringify(data))`.

The only way this could work is to `Flatted.parse(Flatted.stringify(data))`, as it is also for _CircularJSON_ or any other, otherwise there's no granted data integrity.

Also please note this project serializes and deserializes only data compatible with JSON, so that sockets, or anything else with internal classes different from those allowed by JSON standard, won't be serialized and unserialized as expected.


### New in V1: Exact same JSON API

  * Added a [reviver](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse#Syntax) parameter to `.parse(string, reviver)` and revive your own objects.
  * Added a [replacer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#Syntax) and a `space` parameter to `.stringify(object, replacer, space)` for feature parity with JSON signature.


### Compatibility
All ECMAScript engines compatible with `Map`, `Set`, `Object.keys`, and `Array.prototype.reduce` will work, even if polyfilled.


### How does it work ?
While stringifying, all Objects, including Arrays, and strings, are flattened out and replaced as unique index. `*`

Once parsed, all indexes will be replaced through the flattened collection.

<sup><sub>`*` represented as string to avoid conflicts with numbers</sub></sup>

```js
// logic example
var a = [{one: 1}, {two: '2'}];
a[0].a = a;
// a is the main object, will be at index '0'
// {one: 1} is the second object, index '1'
// {two: '2'} the third, in '2', and it has a string
// which will be found at index '3'

Flatted.stringify(a);
// [["1","2"],{"one":1,"a":"0"},{"two":"3"},"2"]
// a[one,two]    {one: 1, a}    {two: '2'}  '2'
```
