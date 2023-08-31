
 cd dist/node_modules/react
 pnpm link --global

    
### Implement an environment for debugging packaging results

### 调试打包结果
pnpm link 
- pnpm link --global
- pnpm link react --global

1. cd dist/node_modules/react
2. pnpm link --global
3. create a demo: npx create-react-app react-demo, cd react-demo
4. in react-demo: pnpm link react --global