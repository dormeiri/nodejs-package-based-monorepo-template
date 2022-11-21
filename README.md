# Package Based Monorepo Template

- [Setup](#setup)
- [Commands](#commands)
  - [Common](#common)
  - [Generating a new package](#generating-a-new-package)
- [Testing packages locally](#testing-packages-locally)
  - [With npm workspaces](#with-npm-workspaces)
  - [With local registry](#with-local-registry)
    - [Publish locally](#publish-locally)
    - [Installing a local package](#installing-a-local-package)

## Setup

```bash
npm ci
```

## Commands

### Common

```bash
npx nx lint <project name>
npx nx test <project name>
npx nx build <project name>
```

_Read more about [running tasks with Nx](https://nx.dev/core-features/run-tasks)_

### Generating a new package

```bash
npm run generate:package <project name>
```

## Testing packages locally

### With [npm workspaces](https://docs.npmjs.com/cli/v9/using-npm/workspaces)

In the root [package.json](package.json) we defined the workspaces.

Every package `dist` folder is a workspaces. So it is linked once you run `npx nx build <project name>`.

You can ensure that the package is linked by searching it in the [node_modules/@%template%](node_modules/@%template%) folder.

To use a package that was built, add it as a dependency to the `package.json` with version `*`, for example:

```json
{
  "dependencies": {
    "@%template%/types": "*"
  }
}
```

_Be aware that when you publish and install it, it doesn't necessarily take the local link!_

### With local registry

We are using [verdaccio](https://verdaccio.org/) for the local registry.

We have some commands to make it even more easy:

```bash
npm run local-registry help
```

See more in [local-registry.mjs](tools/scripts/local-registry.mjs)

#### Publish locally

```bash
npm run local-registry publish <project name>
```

By default, the version is `0.0.0-local.1` with the tag `local` and the registry is `http://localhost:4873`, you can explore more in [publish.mjs](tools/scripts/publish.mjs) and [local-registry.mjs](tools/scripts/local-registry.mjs)

_In local environment, the version is being overwritten if it is already exist._

#### Installing a local package

npm:

```bash
npm install @%template%/<package name>@local --registry=http://localhost:4873
```

yarn:

```bash
yarn add @%template%/<package name>@local --registry=http://localhost:4873
```

## Using the template

### Search and replace

1. Search and replace `@%template%` scopes to another.
2. Search and replace `https://registry.npmjs.org` registry to yours.

### Integrate with CI/CD

#### CI

You can check your changes with Nx tasks, for example you can run:

```bash
npx nx affected --target=lint --base=origin/master
npx nx affected --target=test --base=origin/master
npx nx affected --target=build --base=origin/master
```

#### CD

One way of determine which package should be deployed is by using git tags with the format `<project name>/<version>`. Then, your can run in your CD `npx nx publish $project_name --args="--ver=$version --env=production"`.
