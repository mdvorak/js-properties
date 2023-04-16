# js-java-properties

This is a small library that provides utilities to parse and
manipulate [Java properties](https://docs.oracle.com/javase/9/docs/api/java/util/Properties.html) files.

Intended mostly for the tools that need to modify existing property file, without reformatting the contents.
That is achieved by using string array as a backing storage. If you want only to read the properties,
you should convert it to an object or a `Map` using `toObject(...)` or `toMap(...)` function, respectively.

## Usage

You can install this library using NPM:

```shell
npm install js-java-properties
```

### Parsing

Parses correctly file contents as a string into lines.

```ts
import * as properties from 'js-java-properties'

const props = properties.parse('key1=value1\nkey2 = value2\nkey3: value3')
console.log(props)
// { lines: [ 'key1=value1', 'key2 = value2', 'key3: value3' ] }
```

To read a file from a disk, use standard node `fs` module:

```ts
import fs from 'node:fs'
import * as properties from 'js-java-properties'

const props = properties.parse(fs.readFileSync('file.properties', 'utf-8'))
```

### Stringify

Formats property lines into string.

```ts
import * as properties from 'js-java-properties'

const props = properties.empty()
props.lines.push('key1=value1', 'key2 = value2', 'key3: value3')

const output = properties.stringify(props)
console.log(output)
// 'key1=value1\nkey2 = value2\nkey3: value3\n'
```

### Listing key-value pairs

Iterate over every key-value pair. Note that if file contains duplicate keys,
they are returned here as well.

```ts
import * as properties from 'js-java-properties'

const props = properties.empty()
props.lines.push('key1=value1', 'key2 = value2', 'key3: value3')

for (const {key, value} of properties.listProperties(props)) {
  console.log(`${key}=${value}`)
  // key1=value1
  // key2=value2
  // key3=value3
}
```

### Getting a value by key

Note that this method has `O(n)` complexity for every operation.
Use `toObject` or `toMap` methods to convert it into readable object.

In case there are duplicate keys, last one is returned.

```ts
import * as properties from 'js-java-properties'

const props = properties.empty()
props.lines.push('key1=value1', 'key2 = value2', 'key3: value3')

console.log(properties.getProperty(props, 'key2'))
// 'value2'
```

### Converting to object or map

```ts
import * as properties from 'js-java-properties'

const props = properties.empty()
props.lines.push('key1=value1', 'key2 = value2', 'key3: value3')

console.log(properties.toObject(props))
// { key1: 'value1', key2: 'value2', key3: 'value3' }

console.log(properties.toMap(props))
// Map(3) { 'key1' => 'value1', 'key2' => 'value2', 'key3' => 'value3' }
```

### Setting a value

Adds or replaces given key and value. If value is undefined, it is removed.
Empty string still counts as a value.

If there are duplicate keys in the list, all but first one are removed.

```ts
import * as properties from 'js-java-properties'

const props = properties.empty()
props.lines.push('key1=value1', 'key2 = value2', 'key3: value3')

properties.setProperty(props, 'key2', 'new-value')
console.log(properties.stringify(props))
// 'key1=value1\nkey2 = new-value\nkey3: value3\n'

properties.setProperty(props, 'new-key', 'new-value')
console.log(properties.stringify(props))
// 'key1=value1\nkey2 = new-value\nkey3: value3\nnew-key=new-value\n'

properties.setProperty(props, 'new-key', 'new-value', {separator: ':'})
console.log(properties.stringify(props))
// 'key1=value1\nkey2 = new-value\nkey3: value3\nnew-key:new-value\n'

properties.setProperty(props, 'key3', undefined)
console.log(properties.stringify(props))
// 'key1=value1\nkey2 = new-value\n'
```

### Removing a value

Removes given key and value. If there are duplicate keys in the list, all are removed.

```ts
import * as properties from 'js-java-properties'

const props = properties.empty()
props.lines.push('key1=value1', 'key2 = value2', 'key3: value3')

properties.removeProperty(props, 'key2')
console.log(properties.stringify(props))
// 'key1=value1\nkey3: value3\n'
```

## Development

- Commits must follow [Conventional Commits](https://www.conventionalcommits.org) standard
- Code must conform to eslint and prettier rules
- 100% test coverage is required

### Publishing

Releases are generated using [Release Please](https://github.com/googleapis/release-please).
Package is automatically published to a [npm registry](https://www.npmjs.com/package/js-java-properties) when release is created.

## Contributing

If you would like to contribute to this library, feel free to submit a pull request on GitHub.
