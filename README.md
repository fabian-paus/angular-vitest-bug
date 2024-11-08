# Vitest Angular Bug

When Vitest/Angular is used with pool set to `threads` or `forks`,
we get a strange ESM / CommonJS error.

## Reproduction

```
git clone https://github.com/fabian-paus/angular-vitest-bug.git
cd angular-vitest-bug/analog-project
npm install
npx vitest run
```

Output:

```
TODO: Insert output
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

````js
/// <reference types="vitest" />

import { defineConfig } from 'vite';
import analog from '@analogjs/platform';

// https://vitejs.dev/config/
export default defineConfig(({ mode }) => ({
  build: {
    target: ['es2020'],
  },
  resolve: {
    mainFields: ['module'],
  },
  plugins: [analog()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: ['src/test-setup.ts'],
    include: ['**/*.spec.ts'],
    reporters: ['default'],
    pool: 'threads'
  },
  define: {
    'import.meta.vitest': mode !== 'production',
  },
}));
```
````
