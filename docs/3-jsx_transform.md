## jsx transform

1. Compiling
   1. babel
2. Runtime
   1. jsx or React.createElement
   
###  Implement jsx method
including:
- jsxDEV (dev environment)
- jsx (prod environment)
- React.createElement

### Implement package process
correspond:
- react/jsx-dev-runtime.js (dev)
- react/jsx-runtime.js (prod)
- React
   
### Implement an environment for debugging packaging results

### 调试打包结果
pnpm link 
- pnpm link --global
- pnpm link react --global

1. cd dist/node_modules/react
2. pnpm link --global
3. create a demo: npx create-react-app react-demo, cd react-demo
4. in react-demo: pnpm link react --global