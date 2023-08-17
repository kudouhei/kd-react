
### eslint
install
    - pnpm i eslint -D -w

init
    - npx eslint --init
    - pnpm i -D -w @typescript-eslint/eslint-plugin @typescript-eslint/parser

ts-eslint
    - pnpm i -D -w @typescript-eslint/eslint-plugin
  
### code style - prettier
- pnpm i prettier -D -w

Prettier integrate to eslint
- pnpm i eslint-config-prettier eslint-plugin-prettier -D -w

- eslint-config-prettier: cover ESlint's own rule configuration
- eslint-plugin-prettier: use prettier to take over repair codes

### commits style - husky
install
- pnpm i husky -D -w

init
- npx husky install

pnpm lint, add to husky
- npx husky add .husky/pre-commit "pnpm lint"

commitlint
- pnpm i commitlint @commitlint/cli @commitlint/config-conventional -D -w
- create .commitlintrc.js
- integrate to husky
  - npx husky add .husky/commit-msg "npx --no-install commitlint -e $HUSKY_GIT_PARAMS"
- format
  - <type>: <subject>
