# 建库说明
tetrisrepo主要想实现多包管理前端组件库，所以在此需求上尝试使用lerna实现
## [lerna](https://github.com/lerna/lerna)
```shell
npx lerna init --independent
```
直接在该目录内运行上述命令会生成相应的lerna配置与目录结构，其初始化结构中较为关键的```packages/```，```lerna.json```。
### lerna hoisting
使用**yarn/workspaces**实现lerna hoisting，可以在根目录管理依赖。
```json
// lerna.json
{
  "npmClient": "yarn",
  "useWorkspaces": true
}
```
依赖托管后，lerna的**packages**将会被顶级package.json的**workspaces**覆盖
```json
// package.json
{
  // ...
  "workspaces": [
    "packages/*"
  ]
}
```
### lerna publish config
在lerna.json中配置publish，明确注册表配置，同时配置ignoreChanges避免不必要的版本升级。
```json
{
  "version": "1.1.3",
  "npmClient": "npm",
  "command": {
    "publish": {
      "ignoreChanges": ["ignored-file", "*.md"],
      "message": "chore(release): publish",
      "registry": "https://npm.pkg.github.com"
    },
    "bootstrap": {
      "ignore": "component-*",
      "npmClientArgs": ["--no-package-lock"]
    }
  },
  "packages": ["packages/*"]
}
```
- version: the current version of the repository.
- npmClient: an option to specify a specific client to run commands with (this can also be specified on a per command basis). Change to "yarn" to run all commands with yarn. Defaults to "npm".
- command.publish.ignoreChanges: an array of globs that won't be included in lerna changed/publish. Use this to prevent publishing a new version unnecessarily for changes, such as fixing a README.md typo.
- command.publish.message: a custom commit message when performing version updates for publication. See [@lerna/version](https://github.com/lerna/lerna/tree/main/commands/version#--message-msg) for more details.
- command.publish.registry: use it to set a custom registry url to publish to instead of npmjs.org, you must already be authenticated if required.
- command.bootstrap.ignore: an array of globs that won't be bootstrapped when running the lerna bootstrap command.
- command.bootstrap.npmClientArgs: array of strings that will be passed as arguments directly to npm install during the lerna bootstrap command.
- command.bootstrap.scope: an array of globs that restricts which packages will be bootstrapped when running the lerna bootstrap command.
- packages: Array of globs to use as package locations.

> 除此之外，如果你的包名是带 scope 的，需要在那个包的 package.json 中设置 publishConfig.access 为 "public"。

*某篇文章里提到包名写到scope的问题，这里还不是很理解，后期有用到再看看。*