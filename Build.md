The local dev environment is powered through [Create React App](https://github.com/facebookincubator/create-react-app) which simplifies our build toolchain. However, despite using CRA (Create React App), we do not have any strong dependency towards build tools except for CSS modules. We [patch](https://github.com/rainforestapp/regenwald/blob/fc03836cb73421292c8be41620436c220a54efdf/patches/react-scripts%2B1.0.17.patch#L1) CRA at build time to support CSS modules.

## Starting Dev Environment
All frontned projects should kick start the local dev toolchain when running the following commands
```bash
yarn run start
```

On the initial run, the build tooling will ask which backend environment to test the frontend against. E.g regenwald will ask for the RF Environment (dev, prod, stg, qa), and then a token for a particular user to test against. The build tooling would save these configuration into `configs/default.json`. It will then automatically read from the file on the next run and start without askying any further info. 

### Multiple Backend Configs

Multiple configurations can also be used to test frontend against different environments, by passing an explicit `config` param to the starting script. 
```bash
yarn run start -- --config my-test-config
```
The first time, the build toolchain will ask environment details to test frontend against, and then save them at `configs/my-test-config.json`. Kick starting the build again with the same config will re-read from the config file and use those details. 


### Testing against prod environments
The build toolchain supports testing against the production environments. In regenwald, testing against the production environment requires passing `prod` to the RF environment option. However, the frontend will automatically block any POST / PUT requests by default when testing against prod as a caution measure to not cause any accidental side effects. Recommended approach is to test against staging / local environments when mutating server state.
