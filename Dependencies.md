# Depedencies

### Explicit version numbers
We prefer explicit versions over version ranges when installing packages from npm.

You can do so by adding `--save-exact` to your `npm install`.

Use `npm install some-package --save --save-exact` when you're installing a dependency and `npm install --save-dev --save-exact` when you're installing a dev dependency.

We've had breakages before because of minor patches which introduced bugs. We're humans so this happens. Let's not risk it.

### Avoid installing too many dependencies.
Always ask yourself, do we really need to include that dependency. Ask a colleague if in doubt.

### Explicitly require submodules when available
Some dependencies support submodules. This means that even though the entire library is installed, you can opt to only include a tiny part of it. This helps keep our footprint small.

For example, to include lodash in a module, you'd usually do something like `import _ from 'lodash';`. But since lodash consists of many tiny modules, you should just get the modules you need for this file, like `import pick from 'lodash/object/pick'`.
