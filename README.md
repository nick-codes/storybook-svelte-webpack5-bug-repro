Steps to Reproduce:
```
npx sb@next repro
cd svelte
```

Add to package.json
```
"type": "module"
```

Start storybook to see the immediate crash:

```
% yarn storybook --modern
info @storybook/svelte v6.5.0-alpha.62
info
ERR! Error [ERR_REQUIRE_ESM]: require() of ES Module /Users/nick.codes/svelte/.storybook/main.js from /Users/nick.codes/svelte/node_modules/@storybook/core-common/dist/cjs/utils/interpret-require.js not supported.
ERR! main.js is treated as an ES module file as it is a .js file whose nearest parent package.json contains "type": "module" which declares all .js files in that package scope as ES modules.
ERR! Instead rename main.js to end in .cjs, change the requiring code to use dynamic import() which is available in all CommonJS modules, or change "type": "module" to "type": "commonjs" in /Users/nick.codes/svelte/package.json to treat all .js files as CommonJS (using .mjs for all ES modules instead).
ERR!
ERR!     at interopRequireDefault (/Users/nick.codes/svelte/node_modules/@storybook/core-common/dist/cjs/utils/interpret-require.js:64:16)
ERR!     at serverRequire (/Users/nick.codes/svelte/node_modules/@storybook/core-common/dist/cjs/utils/interpret-require.js:101:10)
ERR!     at getPreviewBuilder (/Users/nick.codes/svelte/node_modules/@storybook/core-server/dist/cjs/utils/get-preview-builder.js:25:55)
ERR!     at buildDevStandalone (/Users/nick.codes/svelte/node_modules/@storybook/core-server/dist/cjs/build-dev.js:104:71)
ERR!     at async buildDev (/Users/nick.codes/svelte/node_modules/@storybook/core-server/dist/cjs/build-dev.js:164:5)
ERR!  Error [ERR_REQUIRE_ESM]: require() of ES Module /Users/nick.codes/svelte/.storybook/main.js from /Users/nick.codes/svelte/node_modules/@storybook/core-common/dist/cjs/utils/interpret-require.js not supported.
ERR! main.js is treated as an ES module file as it is a .js file whose nearest parent package.json contains "type": "module" which declares all .js files in that package scope as ES modules.
ERR! Instead rename main.js to end in .cjs, change the requiring code to use dynamic import() which is available in all CommonJS modules, or change "type": "module" to "type": "commonjs" in /Users/nick.codes/svelte/package.json to treat all .js files as CommonJS (using .mjs for all ES modules instead).
ERR!
ERR!     at interopRequireDefault (/Users/nick.codes/svelte/node_modules/@storybook/core-common/dist/cjs/utils/interpret-require.js:64:16)
ERR!     at serverRequire (/Users/nick.codes/svelte/node_modules/@storybook/core-common/dist/cjs/utils/interpret-require.js:101:10)
ERR!     at getPreviewBuilder (/Users/nick.codes/svelte/node_modules/@storybook/core-server/dist/cjs/utils/get-preview-builder.js:25:55)
ERR!     at buildDevStandalone (/Users/nick.codes/svelte/node_modules/@storybook/core-server/dist/cjs/build-dev.js:104:71)
ERR!     at async buildDev (/Users/nick.codes/svelte/node_modules/@storybook/core-server/dist/cjs/build-dev.js:164:5) {
ERR!   code: 'ERR_REQUIRE_ESM'
ERR! }

WARN Broken build, fix the error above.
WARN You may need to refresh the browser.
```

Add a package.json to .storybook directory so Webpack knows that is CJS not an ESM:
```
echo '{"type":"common"}' > .storybook/package.json
```

Start storybook and note that it works:
```
yarn storybook
```

Change to the Webpack5 builder by installing the packages:
```
yarn remove @storybook/builder-webpack4 @storybook/manager-webpack4
yarn add -D @storybook/builder-webpack5 @storybook/manager-webpack5
```

Then add the config to .storybook/main.js
```
  core: {
    builder: 'webpack5',
  },
```

Run it now with Webpack5
```
yarn storybook
```

Note that storybook never loads. Open the console to see:

```
Uncaught ReferenceError: require is not defined
    at Object../generated-stories-entry.js (generated-stories-entry.js:3:1)
    at __webpack_require__ (bootstrap:24:1)
    at __webpack_exec__ (generated-stories-entry.js:6:1)
    at generated-stories-entry.js:6:1
    at Function.__webpack_require__.O (chunk loaded:23:1)
    at generated-stories-entry.js:6:1
    at webpackJsonpCallback (jsonp chunk loading:560:1)
    at main.iframe.bundle.js:1:73
```

Remove the "type":"module" from the top level package.json and restart storybook.

Note that the require is no longer crashing the app.
