# Coding guidelines & practices 
To ensure code quality and bug-free code we should follow some guidelines.

### Set up ESlint
Eslint can quickly finds problems in your code and also suggests solutions to fix these problems.
- install
    - pnpm i eslint -D -w
- init
    - npx eslint --init
    - pnpm i -D -w @typescript-eslint/eslint-plugin @typescript-eslint/parser
  
### Code style - prettier
Prettier is an opinionated code formatter. It enforces a consistent style by parsing your code and re-printing it with its own rules
- install
  - pnpm i prettier -D -w
- Prettier integrate to eslint
  - pnpm i eslint-config-prettier eslint-plugin-prettier -D -w
    - eslint-config-prettier: cover ESlint's own rule configuration
    - eslint-plugin-prettier: use prettier to take over repair codes
  ```
  {
    "printWidth": 80,
    "tabWidth": 2,
    "javascript.preferences.quoteStyle": "single",
    "useTabs": true,
    "singleQuote": true,
    "semi": true,
    "trailingComma": "none",
    "bracketSpacing": true
  }
  ```

### Commits style
#### 1. Husky
Husky improves your commits.

- install
  - pnpm i husky -D -w
- init
  - npx husky install
    - create `.husky/dir` and enable git hooks 
- Add a hook: pnpm lint, add to husky
  - npx husky add .husky/pre-commit "pnpm lint"
    - To add a command to a hook or create a new one, use husky add <file> [cmd]

#### 2. Commitlint
Commitlint is a tool that lints your commit messages and makes sure they follow a set of rules. It runs as a husky pre-commit hook, that is, it runs before the code is committed and blocks the commit in case it fails the lint checks.

- install
  - pnpm i commitlint @commitlint/cli @commitlint/config-conventional -D -w
- create `.commitlintrc.js` config file
  ```
  module.exports = {
    extends: ['@commitlint/config-conventional'],
  };
  ```
- integrate to husky
  - npx husky add .husky/commit-msg "npx --no-install commitlint -e $HUSKY_GIT_PARAMS"
- format: `<type>: <subject>`
  - feat: add new feature
  - fix: fix bug
  - chore: changes do not affect functionality
  - docs: documents
  - perf: performance
  - refactor: refactor
  - test: test

### Typescript
Typescript compiler uses tsconfig.json to get configuration options fro generating Javascript code from Typescript source code.
- create tsconfig.json
  -  npm install typescript -g
  -  tsc --init 
  ```
  {
    "compileOnSave": true,
    "compilerOptions": {
      "target": "ESNext",
      "useDefineForClassFields": true,
      "module": "ESNext",
      "lib": ["ESNext", "DOM"],
      "moduleResolution": "Node",
      "strict": true,
      "sourceMap": true,
      "resolveJsonModule": true,
      "isolatedModules": true,
      "esModuleInterop": true,
      "noEmit": true,
      "noUnusedLocals": true,
      "noUnusedParameters": true,
      "noImplicitReturns": false,
      "skipLibCheck": true,
      "baseUrl": "./src",
    }
  }
  ```

### Rollup
Rollup is a module bundler for JavaScript which compiles small pieces of code into something larger and more complex, such as a library or application

- install
  - pnpm i -D -w rollup
  ```
  export default {
    input: 'src/main.js', // entry point
    output: {
      file: './dist/bundle.js', // output
      format: 'umd', 
      name: 'pkg name'
    },
    plugins: [], // plugins config
    external: ['lodash'], // external module
  };
  ```

