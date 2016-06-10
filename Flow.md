# Flow

Flow is a static type checker for Javascript. We use it extensively throughout our code base. JS files have to be explicitly marked to be type checked by flow. To enable flow, add the following comment at the start of the file:
```
// @flow
```

## Weak Mode

Flow by default runs in strong mode, in which it will throw an error if it can not deduce a type of a function or object. Flow can also run in weak mode, in which it will assume the type to be `any` if it can not deduce it. This can be enabled by adding the following at the start of the file.
```
// @flow weak
```
We only enable weak mode for old files in the codebase to get good flow code coverage without touching old code.

## External types

Flow allows importing external types from npm packages. ImmutableJS is one npm package which exports flow types. The import syntax is identical to ES6 import syntax except with a type modifier:
```
import type { Map, Record, List } from 'immutable';
```

## Global Variables

Flow can complain about global variables when they are dynamically injected by a package or framework. We internally use Jest, and that exports many global functions such as `describe`, `beforeEach` and so on. We explicitly declare these types in the `interfaces` folder. The interface file for jest at `interfaces/jest.js` looks like this:
```
declare var jest: {
  dontMock(name: string): void;
};

declare var describe: (name: string, cb: () => void) => void;
declare var beforeEach: (cb: () => void) => void;
declare var afterEach: (cb: () => void) => void;
declare var expect: (condition: any) => any;
declare var it: (name: string, cb: () => void) => void;
```

## String literal types

Types in flow can be restricted to only a few possible values. For example, the following function takes a message of type string, and level of type string which can only be 'normal' or 'error':
```
function log(message: string, logLevel: 'normal' | 'error') { ... }
```

### $Keys to restrict type to the keys of another type

Sometimes it can be super useful to restrict value of a string parameter to keys of on object. For example, Map type from ImmutableJS by default maps keys of type `string` to values of type `any`. However, a Map can be restrict to only map keys which exist in another type:

```
import { fromJS }
import type { Map } from 'immutable';
type User = { id: number, name: string }
type UserMap = Map<$Keys<User>, any>;

var user: User = { id: 123, name: 'test' };
var userMap: UserMap = fromJS(user);

// following line will throw an error
userMap.set('unknown_key', 20);

```

### ES6 Class static fields

Flow does not by default understand latest ES6 proposals that are not yet finalized such as static class fields & class instance instance fields. This have to be explicitly enabled in `.flowconfig` file at the root of the project:
```
[options]

esproposal.class_static_fields=enable
esproposal.class_instance_fields=enable
```

Flow is very strict with JS syntax compared to Babel, and can complain any deviation from the spec. One pitfall is not adding a semi-colon after declaring static field. The following will throw a syntax error from flow:
```
static test = 123
constructor() { ... }
```
The workaround is to add semi-colon at the end of static field declaration.
