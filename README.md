# node-yarnpnp-esm

## Run

`yarn node index.js` or `yarn start`

## Step-by-step

```
% mkdir node-yarnpnp-esm && cd $_
% yarn set version latest
➤ YN0000: Retrieving https://repo.yarnpkg.com/3.2.0/packages/yarnpkg-cli/bin/yarn.js
➤ YN0000: Saving the new release in .yarn/releases/yarn-3.2.0.cjs
➤ YN0000: Done in 1s 467ms
% yarn --version
3.2.0
% yarn init
{
  name: 'node-yarnpnp-esm',
  packageManager: 'yarn@3.2.0'
}
% yarn config set pnpEnableEsmLoader true
➤ YN0000: Successfully set pnpEnableEsmLoader to true
% cat .yarnrc.yml
pnpEnableEsmLoader: true
% yarn add rxjs
➤ YN0000: ┌ Resolution step
➤ YN0000: └ Completed in 1s 30ms
➤ YN0000: ┌ Fetch step
➤ YN0013: │ rxjs@npm:7.5.5 can't be found in the cache and will be fetched from the remote registry
➤ YN0013: │ tslib@npm:2.3.1 can't be found in the cache and will be fetched from the remote registry
➤ YN0000: └ Completed
➤ YN0000: ┌ Link step
➤ YN0000: │ ESM support for PnP uses the experimental loader API and is therefore experimental
➤ YN0000: └ Completed
➤ YN0000: Done with warnings in 1s 110ms
% ls -1a
.
..
.editorconfig
.git
.gitignore
.pnp.cjs
.pnp.loader.mjs
.yarn
.yarnrc.yml
README.md
index.mjs
package.json
yarn.lock
% cat <<EOT > index.mjs
import { of } from "rxjs"
import { map } from "rxjs/operators"

let stream$ = of(1,2,3).pipe(
  map(x => x + "!!!")
)

stream$.subscribe((val) => {
  console.log(val) // 1!!! 2!!! 3!!!
})
EOT
% yarn node index.mjs
1!!!
2!!!
3!!!
```

## Links

- I finally set `pnpEnableEsmLoader` after reading https://github.com/yarnpkg/berry/issues/3900#issue-1086802753
  - with this setting, `yarn add <package>` will produce `.pnp.loader.mjs`
  - https://yarnpkg.com/configuration/yarnrc#pnpEnableEsmLoader
- [🎯 ESM Support for PnP · Issue #638 · yarnpkg/berry](https://github.com/yarnpkg/berry/issues/638)
- https://github.com/yarnpkg/berry/issues/1149#issuecomment-1072836588
- https://yarnpkg.com/features/pnp
- https://yarnpkg.com/features/pnp#compatibility-table
- https://yarnpkg.com/getting-started/migration#switching-to-plugnplay
- https://yarnpkg.com/getting-started/editor-sdks#vscode
- https://next.yarnpkg.com/advanced/pnpify
- https://dev.to/arcanis/yarn-31-corepack-esm-pnpm-optional-packages--3hak
- https://www.reddit.com/r/javascript/comments/os2pp4/yarn_30_performances_esbuild_better_patches/
- Yeah, we're using it at a fairly large scale at Uber. Our monorepo has about 1000 local packages in it and depends on thousands more libraries from NPM. https://www.reddit.com/r/javascript/comments/qfknkk/comment/hi0dtmg/
- typescript setup with webpack https://stackoverflow.com/questions/69325144/compiling-typescript-with-dependencies-installed-with-yarn/69784550#69784550
- ts-node https://github.com/nodejs/loaders/issues/56
- make yarn fallback to the filesystem using `link:protocol` https://stackoverflow.com/questions/69010298/yarn-pnp-fallback-to-default-require
- https://www.linkedin.com/pulse/yarn-3-vs-npm-8-dev-teams-2022-alex-alksne/
- VSCode https://psidium.github.io/lerna/monorepo/yarn/nodejs/pnp/zero-install/2021/08/23/migrating-a-monorepo-from-lerna-to-yarn-3.html
- `npx create-react-app your-app-name --use-pnp` https://medium.com/free-code-camp/getting-rid-of-node-modules-with-yarn-plugn-play-a490e5e747d7
- you can disable PnP setting nodeLinker: node-modules in your .yarnrc.yml file https://github.com/yarnpkg/berry/issues/638#issuecomment-895497626
- adding `"type": "module"` to package.json allows using `.js` extension instead of `.mjs`.
