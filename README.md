# seedlings
Create some seed data, via json in a flat format that can be batch loaded

[![stability-unstable](https://img.shields.io/badge/stability-unstable-yellow.svg)][stability]
[![Build Status](https://circleci.com/gh/orangemug/seedlings.png?style=shield)][circleci]
[![Test Coverage](https://codeclimate.com/github/orangemug/seedlings/badges/coverage.svg)][codeclimate]
[![Dependency Status](https://david-dm.org/orangemug/seedlings.svg)][dm-prod]
[![Dev Dependency Status](https://david-dm.org/orangemug/seedlings/dev-status.svg)][dm-dev]

[stability]:   https://github.com/orangemug/seedlings#unstable
[circleci]:    https://circleci.com/gh/orangemug/seedlings
[codeclimate]: https://codeclimate.com/github/orangemug/seedlings/coverage
[dm-prod]:     https://david-dm.org/orangemug/seedlings
[dm-dev]:      https://david-dm.org/orangemug/seedlings#info=devDependencies


## Example
You have two files with seed data

`./users.json`
```json
[
  {
    "id": "{%users.id.1%}",
    "name": "Bob"
  }
]
```

`./posts.json`
```json
[
  {
    "id": "{%posts.id.1%}",
    "owner": "{%users.id.1%}",
    "title": "Hello World",
    "content": "Hi everyone!"
  }
]
```

Notice the special keys of the format

```
{%type:id%}
```

Where `id` is any string. The same string will produce the same resulting id in the final output

`type` groups the `id` for example in `incremental` mode the following

```json
{
  "id":   "{%users:1%}",
  "post": "{%posts:1%}"
}
```

Would become

```json
{
  "id":   1,
  "post": 1
}
```

Now lets create a new instance of seedlings, using the incremental id generator

```js
var seeder = seedlings(seedlings.incremental);

var out1 = seeder.parse(require("./users.json"));
assert.deepEqual(out1, [
  {
    "id": 1,
    "name": "Bob"
  }
]);

var out2 = seeder.parse(require("./posts.json"));
assert.deepEqual(out2, [
  {
    "id": "{%posts.id.1%}",
    "owner": "{%users.id.1%}",
    "title": "Hello World",
    "content": "Hi everyone!"
  }
]);
```

If you're loading into a NoSQL database chances are you don't need to load each file as a separate batch

## API
Create a new instance with

    var seeder = seedlings([id_generator_function]);

Where `id_generator_function` is any function that returns a unique identifier

For example 

We also provide a wrapped `node-uuid` that produces the same uuid on each run

```js
var outNoSQL = seeder.parse(
  require("./posts-no-sql.json")
  require("./users-no-sql.json")
);

assert.deepEqual(outNoSQL, [
  {
    "id": 1,
    "type": "users"
    "name": "Bob"
  },
  {
    "id": "{%posts.id.1%}",
    "type": "posts"
    "owner": "{%users.id.1%}",
    "title": "Hello World",
    "content": "Hi everyone!"
  }
])
```

## `id_generator_function`
The lib comes with 2 `id_generator_function`s built in

 * incremental (default)
 * uuid 

Both incremental and uuid will product the same output given the same input

To use the uuid `id_generator_function`

```js
var seeder = seedlings(require("seedlings/uuid"))
var outUUID = seeder([
  {id: "{%test.uuid%}"
])
assert.deepEqual(outUUID, [
  {id: "kljdksldflkdsjflkdsjflkj"}
]);
```


## License
[MIT](LICENSE)
