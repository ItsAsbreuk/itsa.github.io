---
module: itsa.build
version: 0.0.1
modulesize: 13.58
maintainer: Marco Asbreuk
title: Root of the standard distribution
intro: "The itsa.build module provides the main root `ITSA` namespace and aggregates all the modules that make for the standard distribution of ITSA"
---
#The Basics#

To provide a convenient starting point for the usage of ITSA, we have created a standard distribution that collects the main resources of ITSA into one package and makes their methods accessible under the `ITSA` global namespace.

Though many applications can benefit from using the standard distribution, ITSA is not a monolithic framework.  Its separate modules can be combined in multiple ways. All of the modules are self-contained and take care of their own dependencies and make no reference to the global `ITSA` namespaced provided by this aggregator.

The *standard distribution* is packed into a bundle using [Browserify](http://browserify.org/) so it can be loaded in a single `<script src=" /* .... */  dist/ITSA.js"></script>` tag.  It also provides a `package.json` file so that it, and all of ts dependencies, can be installed via `npm install`.

The examples contained in the documentation all make use of the *standard distribution* which is rooted in this aggregator.  But it is not the only option.  For example, the [Simple Menu](../parcel/menu.html) example starts with:

```js
var ITSA = require('ITSA');
ITSA.ready().then(
	function() {
		var Menu = ITSA.Parcel.subClass({
		    // ...
```

Should the developer choose to use the `parcel` module directly, it is easy to do so:

```js
var Parcel = require('parcel')(window);
var Menu = Parcel.subClass({
    // ....
```

Many of the modules export functions that expect a `window` argument to be passed.  Being designed to work both in the client and the server, modules don't expect to have a global `window` available, thus, those that do need access to some sort of `window` (real or emulated) expect to receive one.

The *standard distribution* contains a minimal DOM emulator which automatically uses when not running in the server.  This allows the developer to use other DOM emulators besides the one provided, when running in the server.

The aggregator is wrapped in this function:

```js
(function (window) {

// .. requires for all the modules.

})(global.window || require('fake-dom')());
```
The wrapper function is called with `global.window` as its argument, which is the way Browserify exposes the browser's own `window` object.   If that is not available, it will `require` our own basic DOM emulator, `fake-dom`.  None of these are mandatory, the developer is free to use other Common-JS module bundler or DOM emulator.

Note that our DOM emulator is not a full featured one.  ITSA makes use of a very small number of DOM methods and `fake-dom` provides only those, which allows it to be very small and highly efficient.  The `fake-dom` module is not included in the browser bundle since it is only expected to be used on the server-side.