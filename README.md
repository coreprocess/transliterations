# Transliterations

[![NPM Version](https://img.shields.io/npm/v/transliterations.svg)](https://www.npmjs.com/package/transliterations)
[![NPM Download](https://img.shields.io/npm/dm/transliterations.svg)](https://www.npmjs.com/package/transliterations)
[![License](https://img.shields.io/npm/l/transliterations.svg)](https://github.com/core-processs/transliterations/blob/master/LICENSE.txt)
[![PRs](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/core-processs/transliterations)

Transliteration / slugify module for node.js, browser, Web Worker, ReactNative and CLI. It provides the ability to transliterate UTF-8 characters into corresponding pure ASCII; so they can be safely displayed, used as URL slugs or file names.

## Installation

```bash
npm install transliterations --save
```
```javascript
import { transliterate as tr, slugify } from 'transliterations';

tr('你好, world!'); // Ni Hao , world!
slugify('你好, world!'); // ni-hao-world
```

### Browser support
`transliterations` has a good browser compatibility with all major browsers (including IE 6-8 if used with `es5-shim`).

### CLI

```bash
npm install transliterations -g

transliterate 你好 # Ni Hao
slugify 你好 # ni-hao
echo 你好 | slugify -S # ni-hao
```

## Usage

### transliterate(str, [options])

Transliterates the string `str` and return the result. Characters which this module doesn't recognise will be defaulted to the placeholder from the `unknown` argument in the configuration option, defaults to `[?]`.

__Options:__ (optional)
```javascript
{
  /* Unicode characters that are not in the database will be replaced with `unknown` */
  unknown: '[?]', // default: [?]
  /* Custom replacement of the strings before transliteration */
  replace: { source1: target1, source2: target2, ... }, // Object form of argument
  replace: [[source1, target1], [source2, target2], ... ], // Array form of argument
  /* Strings in the ignore list will be bypassed from transliteration */
  ignore: [str1, str2] // default: []
}
```

__transliterate.config([optionsObj])__

Bind options globally so any following calls will be using `optoinsObj` by default. If `optionsObj` argument is omitted, it will return current default option object.
```javascript
transliterate.config({ replace: [['你好', 'Hello']] });
transliterate('你好, world!'); // Result: 'Hello, world!'. This equals transliterate('你好, world!', { replace: [['你好', 'Hello']] });
```

__Example__
```javascript
import { transliterate as tr } from 'transliterations';
tr('你好，世界'); // Ni Hao , Shi Jie
tr('Γεια σας, τον κόσμο'); // Geia sas, ton kosmo
tr('안녕하세요, 세계'); // annyeonghaseyo, segye
tr('你好，世界', { replace: {你: 'You'}, ignore: ['好'] }) // You 好, Shi Jie
tr('你好，世界', { replace: [['你', 'You']], ignore: ['好'] }) // You 好, Shi Jie (option in array form)
// or use configurations
tr.config({ replace: [['你', 'You']], ignore: ['好'] });
tr('你好，世界') // You 好, Shi Jie
// get configurations
console.log(tr.config());
```

### slugify(str, [options])
Converts Unicode string to slugs. So it can be safely used in URL or file name.

__Options:__ (optional)
```javascript
{
  /* Whether to force slags to be lowercased */
  lowercase: false, // default: true
  /* Separator of the slug */
  separator: '-', // default: '-'
  /* Custom replacement of the strings before transliteration */
  replace: { source1: target1, source2: target2, ... },
  replace: [[source1, target1], [source2, target2], ... ], // default: []
  /* Strings in the ignore list will be bypassed from transliteration */
  ignore: [str1, str2] // default: []
}
```
If `options` is not provided, it will use the above default values.

__slugify.config([optionsObj])__

Bind options globally so any following calls will be using `optoinsObj` by default. If `optionsObj` argument is omitted, it will return current default option object.
```javascript
slugify.config({ replace: [['你好', 'Hello']] });
slugify('你好, world!'); // Result: 'hello-world'. This equals slugify('你好, world!', { replace: [['你好', 'Hello']] });
```

__Example:__
```javascript
import { slugify } from 'transliterations';
slugify('你好，世界'); // ni-hao-shi-jie
slugify('你好，世界', { lowercase: false, separator: '_' }); // Ni_Hao_Shi_Jie
slugify('你好，世界', { replace: {你好: 'Hello', 世界: 'world'}, separator: '_' }); // hello_world
slugify('你好，世界', { replace: [['你好', 'Hello'], ['世界', 'world']], separator: '_' }); // hello_world (option in array form)
slugify('你好，世界', { ignore: ['你好'] }); // 你好shi-jie
// or use configurations
slugify.config({ lowercase: false, separator: '_' });
slugify('你好，世界'); // Ni_Hao_Shi_Jie
// get configurations
console.log(slugify.config());
```

### Usage in command line
```
➜  ~ transliterate --help
Usage: transliterate <unicode> [options]

Options:
  --version      Show version number                                                       [boolean]
  -u, --unknown  Placeholder for unknown characters                        [string] [default: "[?]"]
  -r, --replace  Custom string replacement                                     [array] [default: []]
  -i, --ignore   String list to ignore                                         [array] [default: []]
  -S, --stdin      Use stdin as input                                     [boolean] [default: false]
  -h, --help     Show help                                                                 [boolean]

Examples:
  transliterate "你好, world!" -r 好=good -r          Replace `,` into `!` and `world` into
  "world=Shi Jie"                                     `shijie`.
                                                      Result: Ni good, Shi Jie!
  transliterate "你好，世界!" -i 你好 -i ，           Ignore `你好` and `，`.
                                                      Result: 你好，Shi Jie !
                                                      Result: 你好,world!
```

```
➜  ~ slugify --help
Usage: slugify <unicode> [options]

Options:
  --version        Show version number                                                     [boolean]
  -l, --lowercase  Use lowercase                                           [boolean] [default: true]
  -s, --separator  Separator of the slug                                     [string] [default: "-"]
  -r, --replace    Custom string replacement                                   [array] [default: []]
  -i, --ignore     String list to ignore                                       [array] [default: []]
  -S, --stdin      Use stdin as input                                     [boolean] [default: false]
  -h, --help       Show help                                                               [boolean]

Examples:
  slugify "你好, world!" -r 好=good -r "world=Shi     Replace `,` into `!` and `world` into
  Jie"                                                `shijie`.
                                                      Result: ni-good-shi-jie
  slugify "你好，世界!" -i 你好 -i ，                 Ignore `你好` and `，`.
                                                      Result: 你好，shi-jie

```

### Caveats
`transliterations` supports almost all common languages whereas there might be quirks in some specific languages. For example, Kanji characters in Japanese will be transliterated as Chinese Pinyin. I couldn't find a better way to distinguish Chinese Hanzi and Japanese Kanji. So if you would like to romanize Japanese Kanji, please consider [kuroshiro](https://github.com/hexenq/kuroshiro.js).

If you find any issues, please raise a GitHub issue. Thanks!

### License
MIT
