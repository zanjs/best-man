# Best-man

# Usage in package.json

From this:
```JSON
{
  "scripts": {
    "build:dist": "NODE_ENV=development webpack --config $npm_package_webpack --progress --colors",
    "test": "NODE_ENV=production karma start"
  }
}
```

To this:
```JSON
{
  "devDependencies": {
    "best-man": "~0.0.1"
  },
  "scripts": {
    "build:dist": "best-man build:dist",
    "build:prod": "best-man build:prod",
    "test": "best-man test"
  },
  "bestScripts": {
    "build:dist": "webpack --config $npm_package_webpack --progress --colors",
    "build:prod": {
      "command": "webpack --config $npm_package_webpack --progress --colors",
      "env": {
        "NODE_ENV": "production"
      }
    },
    "test": {
      "command": "karma start",
      "env": {
        "NODE_ENV": "test"
      }
    }
  }
}
```

_The `bestScripts` script definition can either be a string or sub-object with `command` and `env` attributes. Values defined in the `env` block will override previously set environment variables._

Note that depending on the OS and terminal you're using, dots, spaces or other special characters in the command path may be treated as separators and the command will be parsed wrong.

```JSON
{
  "serve:dist": "./node_modules/.bin/webpack-dev-server --hot --inline --config webpack/development.js"
}
```

To prevent this you need to explicitly wrap the command path with double quotes:

```JSON
{
  "serve:dist": "\"./node_modules/.bin/webpack-dev-server\" --hot --inline --config webpack/development.js"
}
```

# .env File

If you have an `.env` file in your project root it will be loaded on every command.

```
NODE_PATH=./:./lib
NODE_ENV=development
PORT=5000
```

_Environment variables defined in the `bestScripts` script definition will take precedence over `.env` values._

# Shell scripts

Currently, using [bash variables](http://tldp.org/LDP/abs/html/internalvariables.html) (PWD, USER, etc.) is not possible:

``` JSON
  "command": "forever start -l ${PWD}/logs/forever.log -o ${PWD}/logs/out.log -e ${PWD}/logs/errors.log -a index.js",
```

In order to use them, you can create an script file (`.sh`) instead:

`forever.sh`:
``` bash
forever start -l ${PWD}/logs/forever.log -o ${PWD}/logs/out.log -e ${PWD}/logs/errors.log -a index.js
```

`package.json`:
``` javascript
  "command": "./forever.sh"
```

## cli commands

This module expose 2 cli commands:
- `best-man` and,
- a shorter one: `bnr` which is an alias to the former.

The shorter one is useful for cases where you have a script that calls several `best-man` scripts. e.g:

using the normal cli name

```javascript
"scripts": {
  "dev": "shell-exec 'best-man install-hooks' 'best-man watch-client' 'best-man start-dev' 'best-man start-dev-api' 'best-man start-dev-worker' 'best-man start-dev-socket'",
}
```

using the shorter alias

```javascript
"scripts": {
  "dev": "shell-exec 'bnr install-hooks' 'bnr watch-client' 'bnr start-dev' 'bnr start-dev-api' 'bnr start-dev-worker' 'bnr start-dev-socket'",
}
```

And for silence output, you can use `-s` or verbose `--silence` flags

```
bnr -s watch-client
```

And you can use `-p` or verbose `--path` to specify a custom path of dotenv file

```
bnr --path=/custom/path/to/your/env/vars start-dev
```

Also use `-e` or verbose `--encoding` to specify the encoding of dotenv file

```
bnr --encoding=base64 start-dev
```