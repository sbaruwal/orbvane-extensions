# Orbvane extensions

The registry of extensions written for [Orbvane](https://github.com/sbaruwal/orbvane), the native
code editor for Mac. Orbvane's Extensions view lists everything here (in the ORBVANE section) next
to Open VSX extensions.

Every extension is **built from source by this repository's CI**, from the exact commit listed
in `extensions.json`, for Apple silicon and Intel Macs. Nothing is uploaded by hand, so what
you install is what the linked source builds.

## Adding or updating an extension

1. Write the extension (see the `orbvane-extension` crate and the examples in Orbvane's repository).
   Its `package.json` needs `name`, `publisher`, `version` and `"engines": { "orbvane": "^0.1.0" }`.
   Check it packs: `cargo orbvane package`.
2. Open a pull request adding it to `extensions.json`, keyed by its id (`publisher.name`, lowercase):

   ```json
   {
       "extensions": {
           "you.my-extension": {
               "repository": "https://github.com/you/my-extension",
               "rev": "<the full 40-character commit hash to build>",
               "path": "optional/folder/inside/the/repository"
           }
       }
   }
   ```

3. CI builds it on the pull request. Once a maintainer merges it, CI publishes the release
   `you.my-extension-<version>` and updates `index.json`, and Orbvane offers it.

To publish a new version, bump `version` in your `package.json` and open a pull request changing
`rev`. The same version can't be published twice.

Extensions run as native programs with your user's permissions, like Open VSX extensions do.
Review what you install; maintainers review submissions but can't guarantee them.

## How it works

`cargo orbvane registry` (from Orbvane's `crates/cargo-orbvane`) clones each new or changed
entry at its commit, checks that its id matches its `package.json`, builds it for both
architectures, packs a `.vsix`, and writes `dist/<id>-<version>/` (the package, its README,
`package.json` and icon) plus `dist/index.json`. Entries whose commit didn't change are copied
from the previous `index.json`. The editor reads
`https://raw.githubusercontent.com/<this repository>/main/index.json`.
