# Custom Actions

In this section, we will cover how to create and use custom actions in GitHub Actions. We will explore JavaScript/TypeScript actions, containerized actions, composite actions, and publishing private actions for reuse.

Further reading: https://docs.github.com/en/enterprise-cloud@latest/actions/sharing-automations/creating-actions/about-custom-actions#choosing-a-location-for-your-action

## Writing Custom JavaScript/TypeScript Actions

Custom JavaScript/TypeScript actions allow you to write actions using JavaScript or TypeScript. These actions can be used to automate tasks in your workflows.

### Example: Custom JavaScript Action

1. Create a new directory for your action:

```sh
mkdir -p .github/actions/custom-js-action
```

2. Create an `index.js` file in the action directory:

```js
const core = require('@actions/core');

try {
  const name = core.getInput('name');
  console.log(`Hello, ${name}!`);
} catch (error) {
  core.setFailed(error.message);
}
```

3. Create an `action.yml` file in the action directory:

```yaml
name: 'Custom JavaScript Action'
description: 'A custom action written in JavaScript'
inputs:
  name:
    description: 'The name to greet'
    required: true
runs:
  using: 'node12'
  main: 'index.js'
```

4. Use the custom action in your workflow:

```yaml
jobs:
  custom-js-action:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run custom JavaScript action
        uses: ./.github/actions/custom-js-action
        with:
          name: 'GitHub Actions Workshop'
```

## Writing Containerized Actions

Containerized actions allow you to package your action as a Docker container. This can be useful when your action requires a specific environment or dependencies.

### Example: Containerized Action

1. Create a new directory for your action:

```sh
mkdir -p .github/actions/containerized-action
```

2. Create a `Dockerfile` in the action directory:

```Dockerfile
FROM node:12

COPY . .

RUN npm install

ENTRYPOINT ["node", "/index.js"]
```

3. Create an `index.js` file in the action directory (although we're using Node.js in this example, you can use any language or runtime that can be packaged as a Docker container):

```js
const core = require('@actions/core');

try {
  const name = core.getInput('name');
  console.log(`Hello, ${name}!`);
} catch (error) {
  core.setFailed(error.message);
}
```

4. Create an `action.yml` file in the action directory:

```yaml
name: 'Containerized Action'
description: 'A custom action packaged as a Docker container'
inputs:
  name:
    description: 'The name to greet'
    required: true
runs:
  using: 'docker'
  image: 'Dockerfile'
```

5. Use the containerized action in your workflow:

```yaml
jobs:
  containerized-action:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run containerized action
        uses: ./.github/actions/containerized-action
        with:
          name: 'GitHub Actions Workshop'
```

## Writing Composite Actions

Composite actions allow you to combine multiple steps into a single action. This can help simplify your workflows and make them more reusable.

### Example: Composite Action

1. Create a new directory for your action:

```sh
mkdir -p .github/actions/composite-action
```

2. Create an `action.yml` file in the action directory:

```yaml
name: 'Composite Action'
description: 'A custom composite action'
inputs:
  name:
    description: 'The name to greet'
    required: true
runs:
  using: 'composite'
  steps:
    - name: Print greeting
      run: echo "Hello, ${{ inputs.name }}!"
    - name: Print current date
      run: date
```

3. Use the composite action in your workflow:

```yaml
jobs:
  composite-action:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run composite action
        uses: ./.github/actions/composite-action
        with:
          name: 'GitHub Actions Workshop'
```

## Publishing Private Actions for Reuse

Private actions can be published and reused within your organization. This can help you share common actions across multiple repositories. For this use case, we recommend using a monorepo to store your Action, so other users can easily find it, read its documentation, and use it in their workflows pointing to a specific version, SHA, or branch.

### Use the private action in your workflow:

```yaml
    steps:
      - uses: <your_org>/<action_repo>@<sha/branch/version>
```
