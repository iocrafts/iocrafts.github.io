---
title: "🥣 Puppetchef"
description: "This is the puppetchef homepage."
showSummary: true
summary: "A command-line tool to execute web automation recipes in YAML format."
categories: ["Automation"]
tags: ["JavaScript", "Yaml", "Web-browser", "Puppeteer"]
cascade:
  showReadingTime: true
---

**Puppetchef** is a web automation tool that uses Puppeteer to execute web automation recipes defined in YAML format.
You can find and fork the source code on [GitHub](https://github.com/iocrafts/puppetchef) for personal or collaborative use.

## Installation

You can find and install the package directly from the [npm registry](https://www.npmjs.com/package/puppetchef):

```bash
npm install puppetchef
```

## Usage

```bash
puppetchef <recipe> [options]
```

### Command Line Options

- `<recipe>`: Recipe file in YAML format (required).
- `-c, --conf <file>`: Configuration file (default: `puppetchefrc`).
- `--syntax-check`: Validate recipe only, without executing (default: `false`).

### Environment Variables

- `PUPPETCHEF_LOGLEVEL`: Enables debugging at the specified level (info, debug, error, warn, verbose).
- `PUPPETCHEF_LOGFILE`: Path to the log file (default: stdout) when a debug level is set.

### Configuration File

```json
// puppetchefrc

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

To validate a recipe without executing it, simply run:

```bash 
puppetchef recipe.yaml --syntax-check
```

### Execute Recipes

To execute a recipe, simply run:
```bash 
puppetchef recipe.yaml
```

For additional information or file output, configure the appropriate environment variables:

```bash 
PUPPETCHEF_LOGLEVEL=DEBUG
PUPPETCHEF_LOGFILE=/tmp/puppetchef.log 
puppetchef recipe.yaml
```

### Create Recipes

Template engine is based on Handlebarjs and support `env` and `json` as helper functions.

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


### Write a *plugins* Module

```js
// plugin.js in plugins module

module.exports = {
  customAction: async (page, data = {}) => {
    // Custom automation logic
    await page.locator(data.selector);

    // If you need to return values for later use
    return { x: 20, y: "msg" };
  }
};
```

### Install The Plugin In Your Project

```bash
npm install plugins
```

### Use The Plugin In Your Recipes

```yaml 
name: Example Recipe
url: https://example.com/demo-url
tasks:
  - name: "Custom Action"
    steps:
      - puppetchef.plugins.plugin:
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
