# Repro for esmExternals pages router issue

## Description

This is a repro for an issue with the pages router in Next.js when using `esmExternals: false`.
We're trying to add the `exports` field to the `package.json` of MUI core libraries ([PR](https://github.com/mui/material-ui/pull/41596) | [Docs](https://deploy-preview-41596--material-ui.netlify.app/material-ui/migration/migration-v5/#added-exports-field-to-package-json)).
This app is using the MUI libraries from the [PR](https://github.com/mui/material-ui/pull/41596) build (e.g., https://pkg.csb.dev/mui/material-ui/commit/fb7a4ff2/@mui/material).
When testing the libraries on a Next.js app with pages router and `esmExternals: false`, we get the following error:

```
⨯ TypeError: _mui_utils_deepmerge__WEBPACK_IMPORTED_MODULE_3__ is not a function
```

<details>
<summary>Full error</summary>
  
```
 ⨯ TypeError: _mui_utils_deepmerge__WEBPACK_IMPORTED_MODULE_3__ is not a function
    at createPalette (webpack-internal:///./node_modules/@mui/material/styles/createPalette.mjs:266:27)
    at createTheme (webpack-internal:///./node_modules/@mui/material/styles/createTheme.mjs:47:83)
    at eval (webpack-internal:///./node_modules/@mui/material/styles/defaultTheme.mjs:7:82)
    at ./node_modules/@mui/material/styles/defaultTheme.mjs (/Users/diegoandai/MUI/experiments/esm-test-13-nextjs-pages-mui-exports-ts/.next/server/vendor-chunks/@mui.js:260:1)
    at __webpack_require__ (/Users/diegoandai/MUI/experiments/esm-test-13-nextjs-pages-mui-exports-ts/.next/server/webpack-runtime.js:33:42)
    at eval (webpack-internal:///./node_modules/@mui/material/styles/styled.mjs:8:75)
    at ./node_modules/@mui/material/styles/styled.mjs (/Users/diegoandai/MUI/experiments/esm-test-13-nextjs-pages-mui-exports-ts/.next/server/vendor-chunks/@mui.js:310:1)
    at __webpack_require__ (/Users/diegoandai/MUI/experiments/esm-test-13-nextjs-pages-mui-exports-ts/.next/server/webpack-runtime.js:33:42)
    at eval (webpack-internal:///./node_modules/@mui/material/Button/Button.mjs:13:77)
    at ./node_modules/@mui/material/Button/Button.mjs (/Users/diegoandai/MUI/experiments/esm-test-13-nextjs-pages-mui-exports-ts/.next/server/vendor-chunks/@mui.js:20:1)
    at __webpack_require__ (/Users/diegoandai/MUI/experiments/esm-test-13-nextjs-pages-mui-exports-ts/.next/server/webpack-runtime.js:33:42)
    at eval (webpack-internal:///./node_modules/@mui/material/Button/index.mjs:6:69)
    at ./node_modules/@mui/material/Button/index.mjs (/Users/diegoandai/MUI/experiments/esm-test-13-nextjs-pages-mui-exports-ts/.next/server/vendor-chunks/@mui.js:40:1)
    at __webpack_require__ (/Users/diegoandai/MUI/experiments/esm-test-13-nextjs-pages-mui-exports-ts/.next/server/webpack-runtime.js:33:42)
    at __barrel_optimize__?names=Button!=!./node_modules/@mui/material/index.mjs (/Users/diegoandai/MUI/experiments/esm-test-13-nextjs-pages-mui-exports-ts/.next/server/pages/index.js:26:75) {
  page: '/'
}
 GET / 500 in 128ms
```

</details>

This seems related to default import/export interop between esm and cjs: https://www.typescriptlang.org/docs/handbook/modules/appendices/esm-cjs-interop.html

## Steps to reproduce

1. Clone this repo
2. Run `npm install`
3. Run `npm run dev`
4. Open `http://localhost:3000` in the browser
5. You should see the error

If you remove the `esmExternals: false` (or set it to true) from `next.config.js`, the error goes away.

## Expected behavior

The app should work with fine `esmExternals: false`.

## Notes

- I've tested with Next.js `14.2.3`, `13.5.1`, and `13.4.0`.
- I've tried removing internal default imports from one MUI library to another (e.g. importing `@mui/utils` from `@mui/system`), but the error then shows up when importing other CJS libraries from the MUI libraries (e.g. importing from emotion).
