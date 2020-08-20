# react-native-obfuscator

Obfuscate selected source files when building for React Native. 


The original package [react-native-obfuscating-transformer](https://github.com/javascript-obfuscator/react-native-obfuscating-transformer) seems to be broken. So I fixed it.


## Installation

    npm i git+https://github.com/AustinZuniga/react-native-obfuscator.git


**Install needed npm packages**

    npm i babylon babel-traverse

## Usage

### React Native >= 0.59

#### /metro.config.js

```js
module.exports = {
  transformer: {
    getTransformOptions: async () => ({
      transform: {
        experimentalImportSupport: false,
        inlineRequires: false,
      },
    }),
    babelTransformerPath: require.resolve("./transformer")
  },
};
```

#### /transformer.js
> best working obfuscatorOptions after several test.

```js
const obfuscatingTransformer = require("react-native-obfuscator")
const filter = filename => { 
  return filename.startsWith("src");
};

module.exports = obfuscatingTransformer({
  obfuscatorOptions:{
    compact: true, // default true
    controlFlowFlattening: false, // default false
    controlFlowFlatteningThreshold: 0.75, // default 0.75
    deadCodeInjection: false, // default false
    deadCodeInjectionThreshold: 1, // default 0.4
    debugProtection: false,  // default false
    debugProtectionInterval: false, // default false
    disableConsoleOutput: true, 
    identifierNamesGenerator: 'hexadecimal', // default hexadecimal
    log: false,  // default false
    renameGlobals: true, // default false
    rotateStringArray: true, // default true
    seed: 0,// default 0
    selfDefending: false,  // default true: not working if true
    shuffleStringArray: true, // default true
    sourceMapMode: 'separate', // default seperate
    splitStrings: true,  // default false
    splitStringsChunckLength: 10, // default 10
    stringArray: true, // default true
    stringArrayEncoding: 'rc4', // default false
    stringArrayThreshold: 0.75, // default 0.8
    target: 'browser', // default browser
    transformObjectKeys: true, // default false
    unicodeEscapeSequence: true
  },
  upstreamTransformer: require('metro-react-native-babel-transformer'),
  emitObfuscatedFiles: false,
  enableInDevelopment: false,
  filter: filter,
  trace: true
})
```

### React Native < 0.59

### /rn-cli.config.js

```dif
 module.exports = {
+  transformer: {
+    babelTransformerPath: require.resolve("./transformer")
+  },
 }
```

#### /transformer.js

```js
const obfuscatingTransformer = require("react-native-obfuscator")

module.exports = obfuscatingTransformer({
  /* options */
})
```

## Configuration

Options are:

### `upstreamTransformer: MetroTransformer`

Defines what the first pass of code transformation is. If you don't use a custom transformer already,
you don't need to set this option.

TypeScript example:

```dif
 const obfuscatingTransformer = require('react-native-obfuscator')
+ const typescriptTransformer = require('react-native-typescript-transformer')

 module.exports = obfuscatingTransformer({
+  upstreamTransformer: typescriptTransformer
 })
```

#### Default value: `require('metro/src/transformer')`

### `filter: (filename: string, source: string) => boolean`

Returns true for any files that should be obfuscated and false for any files which should not be obfuscated.

By default, it obfuscates all files in `src/**/*`

### `obfuscatorOptions: ObfuscatorOptions`

**Warning** — Not all options are guaranteed to produce working code. In particular, `stringArray` definitely breaks builds.

See the [javascript-obfuscator docs](https://github.com/javascript-obfuscator/javascript-obfuscator) for more info about what each option does.

```ts
interface ObfuscatorOptions {
  compact?: boolean
  controlFlowFlattening?: boolean
  controlFlowFlatteningThreshold?: 0.75
  deadCodeInjection?: boolean
  deadCodeInjectionThreshold?: 0.4
  debugProtection?: boolean
  debugProtectionInterval?: boolean
  disableConsoleOutput?: boolean
  domainLock?: string[]
  identifierNamesGenerator?: "hexadecimal" | "mangled"
  log?: boolean
  renameGlobals?: boolean
  reservedNames?: string[]
  rotateStringArray?: boolean
  seed?: 0
  selfDefending?: boolean
  sourceMap?: boolean
  sourceMapBaseUrl?: string
  sourceMapFileName?: string
  shuffleStringArray?: boolean
  sourceMapMode?: "separate" | "inline"
  splitStrings?: boolean,
  splitStringsChunckLength: 10, // default 10
  stringArray?: boolean
  stringArrayEncoding?: boolean|string
  stringArrayThreshold?: 0.75
  target?: "browser" | "extension" | "node"
  transformObjectKeys?:boolean
  unicodeEscapeSequence?: boolean
}
```

### `trace: boolean`

if true, prints a list of files being obfuscated

#### Default value: `false`

### `emitObfuscatedFiles: boolean`

if true, emits the obfuscated versions of files alongside their originals, for comparison.

#### Default value: `false`

### `enableInDevelopment: boolean`

If true, enables obfuscation in development mode.

#### Default value: `false`


# Known Issue

* identifierNamesGenerator's not working correctly 
[POSSIBLE SOLUTION](https://github.com/javascript-obfuscator/react-native-obfuscating-transformer/issues/28#issuecomment-607931944)

# Reference 

[react-native-obfuscating-transformer](https://github.com/javascript-obfuscator/react-native-obfuscating-transformer)