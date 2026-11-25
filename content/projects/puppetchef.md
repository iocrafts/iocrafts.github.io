---
title: "Puppetchef – YAML Recipes for Puppeteer Web Automation"
description: "Puppetchef is an open-source Node.js CLI that runs web browser automation recipes written in YAML on top of Puppeteer, with plugin support."
showSummary: true
summary: "A command-line tool to execute web automation recipes in YAML format."
categories: ["CLI", "Automation"]
tags: ["JavaScript", "Node.js", "YAML", "Puppeteer"]
---

**Puppetchef** is a web automation tool that uses Puppeteer to execute web automation recipes defined in YAML format.
You can find and fork the source code on [GitHub](https://github.com/iocrafts/puppetchef) for personal or collaborative use.

[![npm version](https://img.shields.io/npm/v/puppetchef)](https://www.npmjs.com/package/puppetchef)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue)](https://github.com/iocrafts/puppetchef/blob/main/LICENSE)

- **License:** MIT
- **Changelog:** [CHANGELOG.md](https://github.com/iocrafts/puppetchef/blob/main/CHANGELOG.md)
- **Report a bug or request a feature:** [GitHub Issues](https://github.com/iocrafts/puppetchef/issues)
- **Contribute:** pull requests are welcome on [GitHub](https://github.com/iocrafts/puppetchef)

## Installation

You can find and install the package directly from the [npm registry](https://www.npmjs.com/package/puppetchef):

```bash
npm install puppetchef
```

When installed locally in a project, run it with `npx puppetchef`. To have the `puppetchef` command available everywhere, install it globally with `npm install -g puppetchef` instead.

## Usage

```bash
puppetchef <recipe> [options]
```

### Command-Line Options

- `<recipe>`: Recipe file in YAML format (required).
- `-c, --conf <file>`: Configuration file (default: `puppetchefrc`).
- `--syntax-check`: Validate recipe only, without executing (default: `false`).

### Environment Variables

- `PUPPETCHEF_LOGLEVEL`: Enables debugging at the specified level (`info`, `debug`, `error`, `warn`, `verbose`).
- `PUPPETCHEF_LOGFILE`: Path to the log file (default: stdout) when a debug level is set.

### Configuration File

The configuration file, `puppetchefrc` by default, is a JSON object of Puppeteer launch options:

```json
{
    "executablePath": "/usr/local/bin/chromium",
    "headless": false,
    "defaultViewport": {
        "width": 1920,
        "height": 1080
    },
    "downloadBehavior": {
        "policy": "allow",
        "downloadPath": "/tmp"
    }
}
```

## Examples

### Validate Recipes

To validate a recipe without executing it, run:

```bash
puppetchef recipe.yaml --syntax-check
```

### Execute Recipes

To execute a recipe, run:

```bash
puppetchef recipe.yaml
```

For additional information or file output, set the logging environment variables on the same command line:

```bash
PUPPETCHEF_LOGLEVEL=debug PUPPETCHEF_LOGFILE=/tmp/puppetchef.log puppetchef recipe.yaml
```

### Create Recipes

The template engine is based on [Handlebars.js](https://handlebarsjs.com/) and supports `env` and `json` as helper functions.

```yaml
url: "https://example.com"
name: "Login Test"
tasks:
  - name: "Login"
    steps:
      - puppetchef.builtin.common:
          command: "fill_out"
          selector: "#username"
          data: "testuser"
      - puppetchef.builtin.common:
          command: "fill_out"
          selector: "#password"
          data: "{{{ env 'PASS' }}}"
      - puppetchef.builtin.common:
          command: "click"
          selector: "#submit"
```

## Create Your Own Plugin

A plugin is an ordinary npm package that exports one or more async functions (commands). Each command receives the Puppeteer `page` and the step's data.

### How Puppetchef Finds Plugins

Every step key has the form `puppetchef.<package>.<file>`. Puppetchef turns it into `require("<package>/<file>")`:

| Step key | Module loaded |
|---|---|
| `puppetchef.builtin.common` | `builtin/common.js`, bundled with Puppetchef |
| `puppetchef.my-puppetchef-plugin.actions` | `my-puppetchef-plugin/actions.js`, from your project's `node_modules` |

The `command` field of the step then selects which exported function to call. Because the key is split on dots, the package name must not contain a `.`.

### Write the Plugin Package

Create a folder for your plugin, for example `my-puppetchef-plugin/`, with a `package.json` whose `name` is `my-puppetchef-plugin` and an `actions.js` file:

```js
// my-puppetchef-plugin/actions.js

module.exports = {
  customAction: async (page, data = {}) => {
    // Custom automation logic
    await page.locator(data.selector);

    // If you need to return values for later use
    return { x: 20, y: "msg" };
  }
};
```

### Install the Plugin in Your Project

Install the plugin in the same project as Puppetchef, so Node.js can resolve it from Puppetchef's location. A global Puppetchef install will not find plugins installed in a project.

```bash
npm install puppetchef ./my-puppetchef-plugin
```

### Use the Plugin in Your Recipes

```yaml
name: Example Recipe
url: https://example.com/demo-url
tasks:
  - name: "Custom Action"
    steps:
      - puppetchef.my-puppetchef-plugin.actions:
          command: "customAction"
          selector: "#username"
        # Make the return value of customAction available to the following steps
        register: ret
      - puppetchef.builtin.common:
          command: debug
          format: "Debugging: {{ ret.y }}"
        # Conditionals for when to execute this step
        when: "{{ ret.x }} > 0"
```

Run it with `npx puppetchef recipe.yaml` from the project folder.
