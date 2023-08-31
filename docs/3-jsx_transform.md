# JSX transform

## What is JSX
JSX stands for javascript XML. JSX means javascript execute. JSX is the Syntax Extension of javascript. It looks the same as HTML but, it is an inline markup language to create React elements and used to describe what UI should appear.

### The JSX transform
JSX is not supported by browsers. It uses compilers like Babel or Typescript to transform JSX into regular JavaScript which is processed by browsers.

JSX which provides us to write HTML in react is not recognized by the browser out of the box. Therefore react developers depend on compilers similar to Babel or typescript to transform JSX code into regular JavaScript.

With the new transform, one can use JSX without importing react. Also, the compiled output may improve the bundle size which may depend on one's setup.

#### Old JSX transform
The limitation of old JSX transform, we need to import React so that javascript knows what to do with the compiled code.

Let us assume your code looks like as below:
```
   import React from 'react';
   function App() {
      return <div>JSX transform</dive>
   }
```
The compilers convert JSX code into regular javascript as below:
```
   import React from 'react';
   function App() {
      return React.createElement('div', null, 'JSX transform');
   }
```

#### New JSX transform
The new transform solves the above issue by two new entry points to use React that can directly be used by the compiler without any need to transform the JSX code to React.createElement. 

This means it is possible to write JSX without importing the React library at the top level or having React in scope. In auto-runtime mode, JSX is converted to a new entry function, `import {jsx as _jsx} from 'react/jsx-runtime.'`

- Automatically import
   ```
      import {jsx as _jsx} from 'react/jsx-runtime'; //  Automatically import by the compiler
      function App() {
         return _jsx('div', { children: 'JSX transform' });
      }
   ```
- Pass the key as a parameter
The key and ref can be passed through the ... extension operator, which makes it impossible to directly tell if key and ref are being passed in this `<div {... .props} />`.

```
   const props = {
      value: 0,
      key: 'foo'
   };
   <div key="foo" {...props}>
      <span>hello</span>
   </div>;
```
new jsx transform:
```
   const props = {
     value: 0,
     key: 'foo'
   };

   _jsx(
     "div",
     {
       ...props,
       children: [_jsx("span", {
         children: "hello"
       })]
     },
     "foo", // as a parameter
   );

   _createElement(
     "div",
     {
       ...props,
       key: "foo" // as part of props
     }, _jsx("span", {
       children: "hello"
     })
   );
```



###  Implement JSX method
including:
- jsxDEV (dev environment)
- jsx (prod environment)
- React.createElement

```
   /**
   * 
   * @param type 
   * @param config 
   * @param maybeKey
   * @returns return ReactElement
   */
   const jsx = (type: ElementType, config: any, maybeKey: any) => {
      // init
      let key = null;
      let ref = null;
   
      // create props to store attributes 
      const props: Props = {};
   
      // iterate config and extract ref, key attributes
      for (const prop in config) {
         const val = config[prop];
         if (prop === "key") {
            continue;
         }
         if (prop === "ref") {
            if (val !== undefined) {
               ref = val;
            }
            continue;
         }
         // use {...props} pass all the attributes
         if ({}.hasOwnProperty.call(config, prop)) {
            props[prop] = val;
         }
      }
      
      // add maybeKey to key
      if (maybeKey !== undefined) {
         key = "" + maybeKey;
      }
      
      return ReactElement(type, key, ref, props);
   };
```