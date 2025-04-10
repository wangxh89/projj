本fork修复了 现有版本在windows报错的问题
npm i @wangxh88/projj -g
projj init
projj add git@github.com:popomore/projj.git

# Projj

Manage repository easily.



## Why?

How do you manage git repository?

Maybe you create a directory and clone to it. However if you want to clone repository that has same name? Or Do something in every directory like `clean`?

`Projj` provide a structure making it easy.

```
$BASE
|- github.com
|  `- popomore
|     `- projj
`- gitlab.com
   `- popomore
      `- projj
```

And you can `DO` everything in repository by [Hook](#hook).

## Feature

- ✔︎ Add repository using `projj add`
- ✔︎ Command Hook
- ✘ Buildin Hook
- ✔︎ Custom Hook
- ✔︎ Run Hook in All Repositories
- ✔︎ Git Support

## Installation

Install `projj` globally.

```bash
$ npm i projj -g
```

## Usage

### Initialize

```bash
$ projj init
```

Set base directory which repositories will be cloned to, default is `~/projj`.

You can change base directory in `~/.projj/config.json`.

### Add Repository

```bash
$ projj add git@github.com:popomore/projj.git
```

it's just like `git clone`, but the repository will be cached by projj. You can find all repositories in `~/.projj/cache.json`

also support alias which could config at `alias` of `~/.projj/config.json`:

```bash
$ projj add github://popomore/projj
```

### Importing

If you have some repositories in `~/code`, projj can import by `projj import ~/code`.

Or projj can import repositories from `cache.json` when you change laptop by `projj import --cache`

### Find Repository

projj provide a easy way to find the location of your repositories.

```bash
$ projj find [repo]
```

You can set `change_directory` in `~/.projj/config.json` to change directory automatically.

### Sync

`projj sync` will check the repository in cache.json whether exists, the repository will be removed from cache if not exist.

## Hook

Hook is flexible when manage repositories.

### Command Hook

When run command like `projj add`, hook will be run. `preadd` that run before `projj add`, and `postadd` that run after `projj add`.

Config hook in `~/.projj/config.json`

```json
{
  "hooks": {
    "postadd": "cat package.json"
  }
}
```

Then will show the content of the package of repository.

**Only support `add` now**

### Define Hook

You can define own hook.

```json
{
  "hooks": {
    "hook_name": "command"
  }
}
```

For Example, define a hook to show package.

```json
{
  "hooks": {
    "show_package": "cat package.json"
  }
}
```

Then you can use `projj run show_package` to run the hook in current directory.

`Command` can be used in `$PATH`, so you can use global node_modules like `npm`.

```json
{
  "hooks": {
    "npm_install": "npm install"
  }
}
```

### Write Hook

Write a command

```js
// clean
#!/usr/bin/env node

'use strict';

const cp = require('child_process');
const cwd = process.cwd();
const config = JSON.parse(process.env.PROJJ_HOOK_CONFIG);
if (config.node_modules === true) {
  cp.spawn('rm', [ '-rf', 'node_modules' ]);
}
```

You can get `PROJJ_HOOK_CONFIG` from `projj` if you have defined in `~/.projj/config.json`.

```json
{
  "hooks": {
    "clean": "clean"
  },
  "clean": {
    "node_modules": true
  }
}
```

### Run Hook

`projj run clean` in current directory.

`projj runall clean` in every repositories from `cache.json`

## License

[MIT](LICENSE)
