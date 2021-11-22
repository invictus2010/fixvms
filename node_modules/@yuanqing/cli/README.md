# @yuanqing/cli [![npm Version](https://img.shields.io/npm/v/@yuanqing/cli?cacheSeconds=1800)](https://www.npmjs.org/package/@yuanqing/cli) [![build](https://github.com/yuanqing/cli/workflows/build/badge.svg)](https://github.com/yuanqing/cli/actions?query=workflow%3Abuild)

## Quick start

```sh
$ npm install --dev @yuanqing/cli
```

<!-- ```js markdown-interpolate: cat example/my-cli.ts -->
```js
#!/usr/bin/env node

import { createCli } from '@yuanqing/cli'

const config = {
  name: 'my-cli',
  version: '1.0.0'
}
const commandConfig = {
  positionals: [
    {
      type: 'STRING',
      name: 'glob-pattern',
      description: 'Glob of input files.',
      required: true,
    }
  ],
  options: [
    {
      type: 'BOOLEAN',
      name: 'minify',
      aliases: ['m'],
      description: 'Whether to minify the output.',
      default: false
    },
    {
      type: 'STRING',
      name: 'output',
      aliases: ['o'],
      description: "Set the output directory. Defaults to './build'.",
      default: './build'
    },
    {
      type: 'NON_ZERO_POSITIVE_INTEGER',
      name: 'parallel',
      aliases: ['p'],
      description: "Set the maximum number of files to process concurrently. Defaults to '3'.",
      default: 3
    }
  ],
  examples: [
    "'*' --minify",
    "'*' --output './dist'",
    "'*' --parallel 10"
  ]
}

try {
  const result = createCli(config, commandConfig)(process.argv.slice(2))
  if (typeof result !== 'undefined') {
    const { positionals, options, remainder } = result
    console.log(positionals)
    console.log(options)
    console.log(remainder)
  }
} catch (error) {
  console.error(error.message)
  process.exit(1)
}
```
<!-- ``` end -->

Parses positional arguments and options:

```sh
$ my-cli 'src/**/*' --minify --output './dist' --parallel 42
{ globPattern: 'src/**/*' }
{ minify: true, output: './dist', parallel: 42 }
[]
```

Handles option aliases:

```sh
$ my-cli 'src/**/*' -m -o './dist' -p 42
{ globPattern: 'src/**/*' }
{ minify: true, output: './dist', parallel: 42 }
[]
```

Throws an error if any required positional arguments (or options) were not specified:

```sh
$ my-cli
Argument <files> is required
```

Throws an error if any options (or positional arguments) were invalid:

```sh
$ my-cli 'src/**/*' -x
Invalid option: -x
```

```sh
$ my-cli 'src/**/*' --parallel 0
Option --parallel must be a non-zero positive integer but got '0'
```

Uses the configured `default` value for unspecified options (or positional arguments):

```sh
$ my-cli 'src/**/*'
{ globPattern: 'src/**/*' }
{ minify: false, output: './build', parallel: 3 }
[]
```

Handles remainder arguments:

```sh
$ my-cli 'src/**/*' foo -- bar --baz
{ globPattern: 'src/**/*' }
{ minify: false, output: './build', parallel: 3 }
[ 'foo', 'bar', '--baz' ]
```

Prints a nice usage message with `--help` or `-h`:

```sh
$ my-cli --help

  Usage:
    $ my-cli <glob-pattern> [options]

  Arguments:
    <glob-pattern>  Glob of input files.

  Options:
    -h, --help      Print this message.
    -m, --minify    Whether to minify the output.
    -o, --output    Set the output directory. Defaults to './build'.
    -p, --parallel  Set the maximum number of files to process concurrently.
                    Defaults to '3'.
    -v, --version   Print the version.

  Examples:
    $ my-cli '*' --minify
    $ my-cli '*' --output './dist'
    $ my-cli '*' --parallel 10

```

Prints the version with `--version` or `-v`:

```sh
$ my-cli --version
1.0.0
```

## License

[MIT](/LICENSE.md)
