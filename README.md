# OpenAI API Functions for Excel

> Create OpenAI chat completions from Excel formulas

Developer oriented Excel add-in that provides `=OAI.CHAT_COMPLETE()` to call the completions API, and helpers. Use the power of spreadsheets for prompt engineering, prompt decomposition, prototyping systems, and more.

## Why this add-in

- Free - use your own API key
- Confidential - calls go directly to OpenAI's API
- Comprehensive - supports all models and parameters
- Observable - view and use all API request and response data
- Open source

Built with generative AI developers in mind - other products are available which may be more suitable for using generative AI in general spreadsheets. As a rule, we don't include convenience functions for tasks that can be reasonably accomplished using built-in Excel functions.

## Blog

Read our post [Prompt engineering with spreadsheets](https://blog.scottlogic.com/2024/01/10/prompt-engineering-with-spreadsheets.html) on the Scott Logic blog, where you'll also find much more about [artificial intelligence](https://blog.scottlogic.com/category/ai.html) and all things software.

## Installation

The add-in is installed via a "sideloading" process, in which you load the manifest file into Excel, which then loads the add-in code directly from GitHub. It's not published to the Office Store.

First, download the [`manifest.xml`](./manifest.xml) file. You won't need to modify this or download anything else - disregard any such instructions in the following linked instructions.

Follow the [sideloading instructions for your platform](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/test-debug-office-add-ins#sideload-an-office-add-in-for-testing):

- On web, use the "manually sideload" technique.
- On Windows, use the "configure trust manually" option at the relevant step.

Type `=OAI.` in the formula bar or a cell, and you should see the new functions.

<details>
<summary>Uninstallation</summary>

On web, clear your browser's cookies for its domain.

For other platforms, [clear the Office cache](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/clear-cache).

</details>

## Usage

`OAI.CHAT_COMPLETE()` calls the API to create completions. It is typical to setup a sheet containing parameters for the model call. The first column is used as parameter keys and the second as values. The API_KEY parameter is required and its value should be sourced from the OpenAI console.

`OAI.COST()` calculates the billing cost(s) of completion cell(s). Wrap it with `SUM()` to calculate a total.

See the [function metadata](https://github.com/robatwilliams/openai-excel-functions/blob/main/src/functions/functions.json) for full documentation of functions and parameters. Excel's presentation of custom function documentation varies in completeness by platform, but is best in the _Insert Function_ dialog and/or the desktop platform.

<!-- prettier-ignore -->
> [!CAUTION]
> We advise caution to avoid unintentionally incurring excessive costs when working with many completion cells or expensive completions. Changes to dependency cells can cause mass recalculation, and certain actions in Excel can trigger full recalculation. You may wish to switch the calculation mode in Excel from automatic to manual. A maximum of 10 API requests are made in parallel - to cancel waiting or pending requests, undo your change or delete the formula.

## Using completion results

Completions populate the cell with a custom data type which includes the complete API request and response. Its text value is the content of the first completion choice.

To obtain the text value for use in formulas, use the `VALUETOTEXT()` function. To obtain other request/response data, use dot notation to the property path (e.g. `A123.response.model`).

To view the completion data, open the entity card. The `lines` property provides a line-by-line view of the completion content.

To extract a final answer that follows a chain of thought, you could use a formula such as:

```
=TEXTAFTER(VALUETOTEXT(A123), "<!-- END CoT -->" & CHAR(10))
```

## Add-in development

<details>
<summary>Instructions for getting started with development work</summary>

### Prerequisites

If you're on Windows, configure NPM to use a sh-compatible shell, e.g:

```bash
npm config set script-shell "C:\\Program Files\\git\\bin\\bash.exe"
```

Configure your IDE to adhere to the project code formatting. For VSCode, these will be loaded from the workspace settings in `.vscode/`; for other editors/IDEs you'll need to configure equivalently. You may need to unset/disable any personal settings or extensions that interfere.

### Install and run

1. Install the npm dependencies (`npm i`)
1. Generate and install certificates (`npm run install-certs`)
1. Run the local server (`npm start`).

Additionally you may need to navigate to the [root](https://localhost:3000/) in your browser and temporarily trust the newly created cert-key pair.

### Open in Excel web

1. Open a spreadsheet in Excel on the web and append the following query string parameters

```
wdaddindevserverport=3000&wdaddinmanifestfile=manifest-localhost.xml&wdaddinmanifestguid=00aeeb98-f4d9-4db0-a1e6-cdc652c08e34
```

2. If you are prompted to enable Excel developer mode, do so.

You should now be able to use the functions as normal. When you make a change, you'll need to reload the browser window.

### Open in Excel desktop

```bash
npm run sideload:desktop
```

### Key files

| Path | Description |
| --- | --- |
| src/functions/functions.json | The metadata which details each function and references its implementation. |
| src/functions/functions.mjs | The JavaScript functions which implement the functions. |
| shared.html | Root page loaded in the background during add-in startup. |
| manifest-local.xml | A version of manifest.xml which references https://localhost:3000/ for add-in development use. |
| manifest.xml | Configures where the add-in should be loaded from and what features it will make use of. |

</details>
