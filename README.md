# Repro for esmExternals pages router issue

## Description

This is a repro for an issue with the pages router in Next.js when using `esmExternals: false`.
We're trying to add the `exports` field to the `package.json` of MUI core libraries ([PR](https://github.com/mui/material-ui/pull/41596) | [Docs](https://deploy-preview-41596--material-ui.netlify.app/material-ui/migration/migration-v5/#added-exports-field-to-package-json)).
When testing the libraries on a Next.js app with pages router and `esmExternals: false`, we get the following error:

```
⨯ TypeError: _mui_utils_deepmerge__WEBPACK_IMPORTED_MODULE_3__ is not a function
```

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
