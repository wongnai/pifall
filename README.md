# pifall

**`pifall`** is an implementation of `promisifyAll` in terms of Node 8's
included [`util.promisify`](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_promisify_original).

That is, it will iterate over your object's properties (`Reflect.ownKeys`), and
make an `Async`-suffixed copy of any functions it finds. If it runs into a
getter, it will create the `Async` version, which will grab the value from the
getter, and work as normal, if the value ends up being a function. Otherwise it
will throw.

The `Async`-suffixed versions are as returned by `util.promisify`.

In the event that you already have a `foo` and a `fooAsync`, the `fooAsync` will
be overwritten. If it's not writable, or not configurable, `pifall` will make no
attempt to circumvent that, meaning it will throw.

Note that `pifall` operates **in-place** on the object. It does not return
anything, and the original object is augmented with the new methods.

`pifall` will flat-out refuse to promisify a built-in prototype, even it it's in
a prototype chain when the `proto` option is provided. It will also flat-out
refuse to promisify objects whose `typeof` is not `function` or `object`. It
will do this by throwing a `TypeError`.

### Usage

Example:
```
const obj = {
  func (cb) {
    setImmediate(() => {
      cb(null, 'hello');
    });
  }
}

require('pifall')(obj);

obj.funcAsync().then(result => console.assert(result === 'hello'));
```

It can also take a second parameter, an optional options object, currently
supporting one property `proto`. If truthy, this causes `pifall` to include any
properties accessible on the object, including those in its prototype chain.

### License

MIT License. See LICENSE.txt.