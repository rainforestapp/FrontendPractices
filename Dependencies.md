# Dependencies

### Explicit version numbers
We prefer explicit versions over version ranges when installing packages from yarn.

You can do so by using `--exact` (see https://classic.yarnpkg.com/en/docs/cli/add/#toc-yarn-add-exact-e)

Use `yarn add some-package@1.2.3 --exact` when you're installing a dependency and `yarn add some-package@1.2.3 --exact --dev` when you're installing a dev dependency (see yarn add some-package@1.2.3 --exact)

We've had breakages before because of minor patches which introduced bugs. We're humans so this happens. Let's not risk it.

### Avoid installing too many dependencies.
Always ask yourself "Do we really need to include that dependency?" Ask a colleague if in doubt.

### Explicitly require submodules when available
Some dependencies support submodules. This means that even though the entire library is installed, you can opt to only include a tiny part of it. This helps keep our footprint small.

For example, to include lodash in a module, you'd usually do something like `import _ from 'lodash';`. But since lodash consists of many tiny modules, you should just get the modules you need for this file, like `import pick from 'lodash/pick'`.

Avoid using the `import * as` syntax when possible.
Example:
```jsx
import React from 'react';

import type { Node } from 'react
```

__AVOID__ this:
```jsx
import * as React from 'react';
```

### Import order
Imports should only be at the top of the file and in the order of:
- React/Redux modules (react, redux, react-redux, react-router-dom, react-modal, etc)
- Other third party modules
- configs and constants
- utilities
- models
- routes
- components
- styles
- types

Within each of these "categories", you might have multiple imports. They should be ordered first by distance from your file. Categories should be separated by an empty line.

```javascript
// @flow
import React from 'react';
import { useHistory } from 'react-router-dom';
import isEmpty from 'lodash/isEmpty';
import createPersistedState from 'use-persisted-state';

import config from 'app/config';
import { TEST_API_URL } from 'app/constants';
import dateHelper from 'app/utilities/date';

import { fetchTests } from 'app/models/Test/TestActions';

import { Button, Input } from 'app/components/UI';

import styleHelpers from 'app/styles/helpers.module.css';
import styles from './styles.module.css';

import type { Map } from 'immutable';
import type { FormStateType } from 'app/components/Form/WithFormState';
```
