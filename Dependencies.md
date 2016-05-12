# Depedencies

Prefer explicit versions over version ranges when installing new packages from npm. You can do so by adding the `--save-exact`.
Use `npm install some-package --save --save-exact` when you're installing a dependency and `npm install --save-dev --save-exact` when you're installing a dev dependency. We've had breakages before that we couldn't explain to ourselves because a minor version of a plugin broke previous functionality.

- Always ask yourself, do we really need to include that dependency. Right now for example, jquery is a fairly dependency, one that we mostly don’t need, if it wasn’t for backbone (which is also to be phased out)
- Explicitly require the lodash methods you need. Because lodash is CommonJS compatible and consists of a bunch of modules we can require those instead of using the entire lodash namespace all the time. simply do: `import forEach from 'lodash/collection/forEach';` or `const forEach = require('lodash/collection/forEach');`
