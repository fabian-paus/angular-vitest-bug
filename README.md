# Vitest Angular Bug

When Vitest/Angular is used with pool set to `threads` or `forks`,
we get a strange ESM / CommonJS error.

Tested on:

- Windows and Linux
- Node 22.11 and 20.18
- Vitest version 1.6 and 2.1.4

Questions:

- Why does the ESM/CommonJS loading behavior change between different `pool` options?
- How can I run vitest with Angular using the `threads` or `forks` option?
- Does `@analogjs\vitest-angular` declare the wrong type in their `package.json`

## Reproduction

```
git clone https://github.com/fabian-paus/angular-vitest-bug.git
cd angular-vitest-bug/analog-project
npm install
npx vitest run
```

Output:

```
 RUN  v1.6.0 C:/work/temp/angular-vitest-bug/analog-project

 ❯ src/app/app.component.spec.ts (0)

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Suites 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯
 FAIL  src/app/app.component.spec.ts [ src/app/app.component.spec.ts ]
Error: require() of ES Module C:\work\temp\angular-vitest-bug\analog-project\node_modules\@analogjs\vitest-angular\setup-snapshots.js from C:\work\temp\angular-vitest-bug\analog-project\node_modules\@analogjs\vitest-angular\setup-zone.js not supported.
setup-snapshots.js is treated as an ES module file as it is a .js file whose nearest parent package.json contains "type": "module" which declares all .js files in that package scope as ES modules.
Instead either rename setup-snapshots.js to end in .cjs, change the requiring code to use dynamic import() which is available in all CommonJS modules, or change "type": "module" to "type": "commonjs" in \\?\C:\work\temp\angular-vitest-bug\analog-project\node_modules\@analogjs\vitest-angular\package.json to treat all .js files as CommonJS (using .mjs for all ES modules instead).

 ❯ TracingChannel.traceSync node:diagnostics_channel:315:14
 ❯ node_modules/@analogjs/vitest-angular/setup-zone.js:7:1
      5| require("zone.js/plugins/proxy");
      6| require("zone.js/testing");
      7| require("./setup-snapshots.js");
       | ^
      8| /**
      9|  * Patch Vitest's describe/test/beforeEach/afterEach functions so test code

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯Serialized Error: { code: 'ERR_REQUIRE_ESM' }
⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯
 Test Files  1 failed (1)
      Tests  no tests
   Start at  08:17:14
   Duration  2.44s (transform 165ms, setup 0ms, collect 0ms, tests 0ms, environment 1.23s, prepare 310ms)
```

Expected behavior: Vitest runs and executes the test(s).

## Project Setup

```bash
npm create analog@latest # Choose all default options
cd analog-project
npm install
```

Running the test now, succeeds:

```bash
npx vitest run
```

Output:

```
 RUN  v1.6.0 C:/work/temp/angular-vitest-bug/analog-project

 ✓ src/app/app.component.spec.ts (1)
   ✓ AppComponent (1)
     ✓ should create the app

 Test Files  1 passed (1)
      Tests  1 passed (1)
   Start at  08:11:32
   Duration  5.84s (transform 2.76s, setup 3.45s, collect 356ms, tests 42ms, environment 1.27s, prepare 1.62s)
```

### Set Pool to Threads

Now, we set the pool option to threads in `analog-project/vite.config.ts`:

```js
/// <reference types="vitest" />

import { defineConfig } from "vite";
import analog from "@analogjs/platform";

// https://vitejs.dev/config/
export default defineConfig(({ mode }) => ({
  build: {
    target: ["es2020"],
  },
  resolve: {
    mainFields: ["module"],
  },
  plugins: [analog()],
  test: {
    globals: true,
    environment: "jsdom",
    setupFiles: ["src/test-setup.ts"],
    include: ["**/*.spec.ts"],
    reporters: ["default"],
    pool: "threads", // ADDED THIS LINE
  },
  define: {
    "import.meta.vitest": mode !== "production",
  },
}));
```

Executing the tests now:

```bash
npx vitest run
```

Output:

```
 RUN  v1.6.0 C:/work/temp/angular-vitest-bug/analog-project

 ❯ src/app/app.component.spec.ts (0)

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Suites 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯
 FAIL  src/app/app.component.spec.ts [ src/app/app.component.spec.ts ]
Error: require() of ES Module C:\work\temp\angular-vitest-bug\analog-project\node_modules\@analogjs\vitest-angular\setup-snapshots.js from C:\work\temp\angular-vitest-bug\analog-project\node_modules\@analogjs\vitest-angular\setup-zone.js not supported.
setup-snapshots.js is treated as an ES module file as it is a .js file whose nearest parent package.json contains "type": "module" which declares all .js files in that package scope as ES modules.
Instead either rename setup-snapshots.js to end in .cjs, change the requiring code to use dynamic import() which is available in all CommonJS modules, or change "type": "module" to "type": "commonjs" in \\?\C:\work\temp\angular-vitest-bug\analog-project\node_modules\@analogjs\vitest-angular\package.json to treat all .js files as CommonJS (using .mjs for all ES modules instead).

 ❯ TracingChannel.traceSync node:diagnostics_channel:315:14
 ❯ node_modules/@analogjs/vitest-angular/setup-zone.js:7:1
      5| require("zone.js/plugins/proxy");
      6| require("zone.js/testing");
      7| require("./setup-snapshots.js");
       | ^
      8| /**
      9|  * Patch Vitest's describe/test/beforeEach/afterEach functions so test code

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯Serialized Error: { code: 'ERR_REQUIRE_ESM' }
⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯
 Test Files  1 failed (1)
      Tests  no tests
   Start at  08:17:14
   Duration  2.44s (transform 165ms, setup 0ms, collect 0ms, tests 0ms, environment 1.23s, prepare 310ms)
```

### Set Pool to Forks

Setting `pool: "forks"` results in the same error.

### Editing @analogjs\vitest-angular\package.json

As suggested by the error message, we now edit the file `analog-project\node_modules\@analogjs\vitest-angular\package.json`. We change `"type": "module"` to `"type": "commonjs"`:

```json
{
  "name": "@analogjs/vitest-angular",
  "version": "1.9.2",
  "description": "Vitest Builder for Angular",
  "type": "commonjs",
  "author": "Brandon Roberts <robertsbt@gmail.com>",
  "exports": {
...
```

Exceute the tests:

```bash
npx vitest run
```

Output:

```
 RUN  v1.6.0 C:/work/temp/angular-vitest-bug/analog-project

 ✓ src/app/app.component.spec.ts (1)
   ✓ AppComponent (1)
     ✓ should create the app

 Test Files  1 passed (1)
      Tests  1 passed (1)
   Start at  08:24:24
   Duration  4.09s (transform 1.75s, setup 2.26s, collect 288ms, tests 42ms, environment 1.04s, prepare 166ms)
```

While this works, it is not an option to edit files in the `node_modules` folder.

### Rename Configuration to vitest.config.mts

As an alternative, we try to rename the configuration file `vite.config.ts` to `vite.config.mts`.
This has been suggested if you encountered weird ESM behavior.
To test this, we go back to the original `"type": "module"` content in the `node_modules` folder.

```
mv vite.config.ts vite.config.mts
```

Execute

```bash
npx vitest run
```

Output: Same error message as before.
