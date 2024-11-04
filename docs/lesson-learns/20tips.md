---
title: ⓴ TIP FOR WRITING BETTER REACT CODE ✍(◔◡◔)
---
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;React is a library that is widely used in the frontend world at the moment. However, when participating in the real project, if you write code without rules, don't follow the recommended patterns, you will easily encounter the situation that your source code becomes messy, inconsistent, difficult to manage and maintain.
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Therefore, with what I have experienced, along with referencing many different resources in the internet. I will introduce to you 20 tips for writing better React code that I personally find useful.


### ①. Use functional components 

- Components have Two types: a **class component** and a **functional component**.
- Class components:
  - have internal state and life cycle methods.
  - "this" binding
- Functional components:
  - hooks
  - accept props as arguments and return React elements

**The advantages of functional components are :**

- Less code
- Simpler compared to class-based functions
- No “this” binding
- Easy to understand

**Class component:**
```jsx
import React, { Component } from "react";
 
class ClassComponent extends React.Component{
    constructor(){
        super();
        this.state={
            count :0
        };
        this.increase = this.increase.bind(this);
    }
     
   increase(){
       this.setState({count : this.state.count +1});
   }
 
    render(){
        return (
            <div style={{margin:'50px'}}>
               <h1>Welcome to my world </h1>
               <h3>Counter App using Class Component : </h3>
               <h2> {this.state.count}</h2> 
               <button onClick={this.increase}> Add</button>
 
            </div>
        )
    }
}
 
export default ClassComponent;
```

**Functional component:**
```jsx
import React, { useState } from "react";
 
const FunctionalComponent=()=>{
    const [count, setCount] = useState(0);
 
    const increase = () => {
        setCount(count+1);
    }
 
    return (
        <div style={{margin:'50px'}}>
            <h1>Welcome to my world </h1>
            <h3>Counter App using Functional Component : </h3>
          <h2>{count}</h2>
            <button onClick={increase}>Add</button>
        </div>
    )
} 

export default FunctionalComponent;
```
### ②. Follow common naming conventions 

Use **PascalCase** in components, interfaces, or type aliases

```tsx
// React component
const LeftGridPanel = () => {
  ...
}

// Typescript interface
interface AdminUser {
  name: string;
  id: number;
  email: string;
}

// Typescript Type Alias
type TodoList = {
    todos: string[];
    id: number;
    name: string;
}
```

Use **camelCase** for JavaScript data types like variables, arrays, objects, functions, and so on

```js
const getLastDigit = () => { ... }

const userTypes = [ ... ]
```

**Always use camelCase** for prop names or PascalCase if the prop value is a React component.

**Bad**
```jsx
<Component
  UserName="hello"
  phone_number={12345678}
/>
```
**Good**
```jsx
<MyComponent
  userName="hello"
  phoneNumber={12345678}
  Component={SomeComponent}
/>
```

### ③. Keep your Components small 
- A component should ideally only do one thing and do it well. 
- If a component is getting too large or doing too many things a→ break it up into smaller.

- Small component helps:
  - **easily read and understand** the functionality of this component.
  - **easier to update**.
  - **re-usability** is also increased.
  - easy to implement **performance optimizations**.

**Example:**
- Show list of post on search screen. Each post includes: *name, release date, author, short content, tag list, number of favorites*.
- With the above information, depending on the complexity of each component, we can divide it in different ways. Here is an example:

```xml
.
└── PostList/
    └── PostItem/
        ├── Author (avatar + username)
        ├── TagList
        └── FavoriteNumber
```


### ④. Use Linters 
- A linter tool helps **improve code quality**.
- One of the most popular linter tools for JavaScript and React is [ESlint](https://eslint.org/).
- A linter tool helps with **consistency** in a code base. When using a tool like ESLint, you can set the rules you want every developer working on the project to follow.
- The tool **observes your code** and then **notifies** you when a **rule has been broken**.
- Currently, in most projects, we often combine Eslint and **[Prettier](https://prettier.io/)** to help us automatically format code based on predefined rules of eslint.

### ⑤. Always prefer passing objects 

- Is good ways to limit the number of props passed.
- For example: instead of passing each detail of a Student, you can group them:

**Bad**
```jsx
<StudentAccount
    name={user.name}
    email={user.email}
    id={user.id}
/>
```

**Good**
```jsx
// Pass objects
<StudentAccount user={user} />
```

### ⑥. Extract reusable logic into custom hooks 

> Hooks allow us to reuse stateful logic without changing our component hierarchy.

- In a situation that you have to **reuse** the same stateful logic that is already used in another functional component, that's a great time to create **a custom hook**.

**Example**:

- We need to update our UI according to the screen size.
- Keep track of the current window size when resizing the browser window manually.

```jsx
const ScreenDimensions = () => {
  const [windowSize, setWindowSize] = useState({
    width: undefined,
    height: undefined,
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }
    window.addEventListener("resize", handleResize);
    handleResize();
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return (
    <>
      <p>Current screen width: {windowSize.width}</p>
      <p>Current screen height: {windowSize.height}</p>
    </>
  );
};
```

- With above code, the problem will happen when we use the exact logic in another component.
- Of course we could just copy the logic, paste it in and we're done → not a good practice, as you might know from the **[DRY principle](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)  (Don't repeat yourself)**.
- When adjust our logic → do it in all components → it becomes **less** maintainable and more **error** prone.

Let's see how our custom hook would look:

```jsx
const useWindowSize = () => {
  const [windowSize, setWindowSize] = useState({
    width: undefined,
    height: undefined,
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }
    window.addEventListener("resize", handleResize);
    handleResize();
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return windowSize;
};
```

Now let's simply call it inside our ScreenDimensions component:

```jsx
const ScreenDimensions = () => {
  const windowSize = useWindowSize();

  return (
    <>
      <p>Current screen width: {windowSize.width}</p>
      <p>Current screen height: {windowSize.height}</p>
    </>
  );
};
```

This enables us to just call the custom hook in any other component and save the return value:

```jsx
const ResponsiveView = () => {
  const windowSize = useWindowSize();

  return <>{windowSize.width <= 960 ? <SmartphoneView /> : <DesktopView />}</>;
};
```

### ⑦. Implement the useReducer hook earlier 

Issue:

```jsx
const CustomersMap = () => {
  const [isDataLoading, setIsDataLoading] = useState(false)
  const [customersData, setCustomersData] = useState([])
  const [hasError, setHasError] = useState(false)
  const [isHovered, setIsHovered] = useState(false)
  const [hasMapLoaded, setHasMapLoaded] = useState(false)
  const [mapData, setMapData] = useState({})
  const [formData, setFormData] = useState({})
  const [isBtnDisabled, setIsBtnDisabled] = useState(false)

  ...

  return ( ... )
}
```

With above example, we can see:

- **a lot of** different useState hooks.
- the **complexity** of the component is growing.
- **hard for** read and understand the logic of the component.

→ To **increase the legibility** of your component, there is the **useReducer** hook.

The official **React docs** say this about it:

> **useReducer** is usually preferable to **useState** when you have complex state logic that involves multiple sub-values or when the next state depends on the previous one. useReducer also lets you optimize performance for components that trigger deep updates because you can pass dispatch down instead of callbacks.

```jsx
// INITIAL STATE
const initialState = {
  isDataLoading: false,
  customerData: [],
  hasError: false,
  isHovered: false,
  hasMapLoaded: false,
  mapData: {},
  formdata: {},
  isBtnDisabled: false
}

// REDUCER
const reducer = (state, action) => {
  switch (action.type) {
    case 'POPULATE_CUSTOMER_DATA':
      return {
        ...state,
        customerData: action.payload
      }
    case 'LOAD_MAP':
      return {
        ...state,
        hasMapLoaded: true
      }
    ...
    ...
    ...
    default: {
      return state
    }
  }
}

// COMPONENT
const CustomersMap = () => {
  const [state, dispatch] = useReducer(reducer, initialState)

  ...

  return ( ... )
}
```

### ⑧. Use Ternary Operators 

- Avoid multiple if-else blocks, using **Ternary Operators** for more clean code practice.

**Bad**

```jsx
const { role } = user;

if (role === ADMIN) {
  return <AdminUser />;
} else {
  return <NormalUser />;
}
```

**Good**

```jsx
const { role } = user;

return role === ADMIN ? <AdminUser /> : <NormalUser />;
```


### ⑨. Take Advantage of Object Literals 

- Object literals can help make our code **more readable**.

Example: 
  - We want to show three types of users based on their roles. You can’t use ternary because the number of options exceeds two.

**Bad**

```jsx
const { role } = user;

switch (role) {
  case ADMIN:
    return <AdminUser />;
  case EMPLOYEE:
    return <EmployeeUser />;
  case USER:
    return <NormalUser />;
}
```

**Good**

```jsx
const { role } = user;

const components = {
  ADMIN: AdminUser,
  EMPLOYEE: EmployeeUser,
  USER: NormalUser,
};

const Component = components[role];

return <Componenent />;
```

### ⑩. Write a fragment when a div is not needed 

- Always **use Fragment over Div**.
- It keeps the **code clean** and is also beneficial for **performance** because one less node is created in the virtual DOM.

**Bad**

```jsx
return (
  <div>
    <Component1 />
    <Component2 />
    <Component3 />
  </div>
);
```

**Good**

```jsx
return (
  <>
    <Component1 />
    <Component2 />
    <Component3 />
  </>
);
```

### ⑪. Avoid unnecessary rendering by using Memo

- `React.PureComponent` and `Memo` can significantly **improve the performance** of your application. They help us to avoid unnecessary rendering.

**Bad**

```jsx
import React, { useState } from "react";

export const TestMemo = () => {
  const [userName, setUserName] = useState("faisal");
  const [count, setCount] = useState(0);

  const increment = () => setCount((count) => count + 1);

  return (
    <>
      <ChildrenComponent userName={userName} />
      <button onClick={increment}> Increment </button>
    </>
  );
};

const ChildrenComponent = ({ userName }) => {
  console.log("rendered", userName);
  return <div> {userName} </div>;
};
```

The `ChildrenComponent` will be rendered each time you click on the button while it should render only once → unnecessary rendering

**Good**

```jsx
import React, { useState } from "react";

const ChildrenComponent = React.memo(({ userName }) => {
  console.log("rendered");
  return <div> {userName}</div>;
});
```

### ⑫. Remove Js code from JSX 

- Move any JS code out of JSX if that doesn’t serve any purpose of rendering or UI functionality.

**Bad**

```jsx
return (
  <ul>
    {posts.map((post) => (
      <li
        onClick={(event) => {
          console.log(event.target, "clicked!"); // <- THIS IS BAD
        }}
        key={post.id}
      >
        {post.title}
      </li>
    ))}
  </ul>
);
```

**Good**

```jsx
const onClickHandler = (event) => {
  console.log(event.target, "clicked!");
};

return (
  <ul>
    {posts.map((post) => (
      <li onClick={onClickHandler} key={post.id}>
        {" "}
        {post.title}{" "}
      </li>
    ))}
  </ul>
);
```

### ⑬. Import in Order 

- Always try to **import** things in a **certain order**. It improves code **readability**.

**Bad**

```jsx
import React from "react";
import ErrorImg from "../../assets/images/error.png";
import styled from "styled-components/native";
import colors from "../../styles/colors";
import { PropTypes } from "prop-types";
```

The rule of thumb is to keep the import order like this:

- **Built-in**
- **External**
- **Internal**

So the example above becomes:

**Good**

```jsx
import React from "react";

import { PropTypes } from "prop-types";
import styled from "styled-components/native";

import ErrorImg from "../../assets/images/error.png";
import colors from "../../styles/colors";
```

You can config it in `eslintrc.json`

```json
"import/order": [
      "error",
      {
        "groups": ["builtin", "external", "internal", ["parent", "sibling"]],
        "pathGroups": [
          {
            "pattern": "react",
            "group": "external",
            "position": "before"
          }
        ],
        "pathGroupsExcludedImportTypes": ["react"],
        "newlines-between": "never",
        "alphabetize": {
          "order": "asc",
          "caseInsensitive": true
        }
      }
    ]
```

### ⑭. Reserved Prop Naming 
- **Don’t use DOM component prop names** for passing props between components because others might not expect these names.

**Bad**
```jsx
<MyComponent style="dark" />

<MyComponent className="dark" />
```

**Good**
```jsx
<MyComponent variant="fancy" />
```

### ⑮. Underscore in Method Name
**Don't use underscores** in any internal React **method**.
**Bad**
```jsx
const _onClickHandler = () => {
  // do stuff
}
```

**Good**
```jsx
const onClickHandler = () => {
  // do stuff
}
```

### ⑯. Alt Prop
- **Always include** an **alt prop** in your `<img >` tags
- Don’t use `picture` or `image` in your alt property. No need to include that.

**Bad**
```jsx
<img src="hello.jpg" />
<img src="hello.jpg" alt="Picture of me rowing a boat" />
```

**Good**
```jsx
<img src="hello.jpg" alt="Me waving hello" />
```

### ⑰. Use Object Destructuring For Props 
- Instead of passing the props object, use **object destructuring** to pass the prop name. 
- This discards the need to refer to the props object each time you need to use it.

**Bad**
```jsx
const Button = (props) => {
  return <button>{ props.text }</button>;
};
```

**Good**
```jsx
const Button = ({ text }) => {
  return <button>{ text }</button>;
};
```

### ⑱. Separating business logic from component logic 

**What is the Separation of Concerns?**
- It states that logic that performs different actions should not be groupled or combined together.
→ we should separate these two pieces of logic – that is, fetching data and presenting it on the UI – into two **different components**.

To achieve a separation of concerns we have two types of components:
- *Container components*
- *Presentational components*

**Container components**: 
- **Provide, create, or hold data** for the children components.
- The only job of a container component is to handle data. It doesn't consist of any UI of its own.

**Presentational components**:
- Primary responsibility is to **present the data** on the UI.
- They take in the data from the container components.
- These components are **stateless** unless they need their own state for rendering the UI.

→ Such division makes us **easy to access, easy to maintain, and highly reusable**.

**How to Replace Container Components with React Hooks**:
- Since React 16.8.0, easier to build and develop components with functional components and hooks.
- With Hooks, we can handle logic inside a **custom Hook**, example: with hook **usePosts**, we can use a useEffect inside the hook → call API → return post list data.
- Now we can simply remove the container component, passing the container's data to the presentational components as a prop.
- We can directly use this hook inside the **Posts** presentational component.

**Example code demo:**

**usePosts.tsx**
```jsx
import { useEffect, useState } from "react";
import { ISinglePost } from "../Definitions";

export default function usePosts() {
  const [posts, setPosts] = useState<ISinglePost[] | null>(null);
  const [isLoading, setIsLoading] = useState<Boolean>(false);
  const [error, setError] = useState<unknown>();

  useEffect(() => {
    (async () => {
      try {
        setIsLoading(true);
        const resp = await fetch("https://jsonplaceholder.typicode.com/posts");
        const data = await resp.json();
        setPosts(data.filter((post: ISinglePost) => post.userId === 1));
        setIsLoading(false);
      } catch (err) {
        setError(err);
        setIsLoading(false);
      }
    })();
  }, []);

  return {
    isLoading,
    posts,
    error
  };
}
```

**Posts.tsx**
```tsx
/**
 * A presentational component
 */

import { ISinglePost } from "../Definitions";
import usePosts from "../hooks/usePosts";
import SinglePost from "./SinglePost";

export default function Posts(props: { posts: ISinglePost[] }) {
  const { isLoading, posts, error } = usePosts();

  return (
    <ul
      style={{
        display: "flex",
        flexDirection: "column",
        alignItems: "center"
      }}
    >
      {isLoading ? (
        <span>Loading...</span>
      ) : posts ? (
        posts.map((post: ISinglePost) => <SinglePost {...post} />)
      ) : (
        <span>{JSON.stringify(error)}</span>
      )}
    </ul>
  );
}
```

### ⑲. Use a testing library 
- Testing is an essential part of the development process.
- Help you **catch bugs early** and ensure that your code is **reliable and maintainable**.
- Some popular options include [Jest](https://jestjs.io/), [Enzyme](https://enzymejs.github.io/enzyme/), and [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/).

### ⑳. Lazy Loading to improve performance
- Is an **optimization technique** for websites or web applications
- When a user visits a web page, instead of loading the entire page, only a **portion of it renders**. And then, it will **delay** the remaining webpage until the user scrolls to that portion of the web page.
- Benefits of Lazy loading React component:
  - Faster initial loading
  - Better User Experience
  - Less bandwidth consumption
  - Preserving system resources
  - Reduced work for the browser

- `React lazy()` integrated into the core react library itself → easy for us to lazy load react components.

Example:
```jsx
const Artists = React.lazy(() => import('./Artists'));

function MyComponent() {
  return (
    <div>
      <Artists />
    </div>
  );
}
```

### Conclusion
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;I hope the above tips will be really helpful for you. Of course there might be best practices you consider more important that I've missed here. That's great. I'd love to hear what you'd like to add to this lesson. Thank you for taking time for my lesson.
### REFERENCES
1. https://www.loginradius.com/blog/engineering/guest-post/react-best-coding-practices/
2. https://betterprogramming.pub/21-best-practices-for-a-clean-react-project-df788a682fb?gi=17305bcc69d3
3. https://alite-international.com/8-best-react-practices-to-follow/
4. https://kinsta.com/blog/react-best-practices 
5. https://medium.com/@dan_abramov?source=post_page-----7ca2f9a7c7d0-------------------------------- 
6. https://www.freecodecamp.org/news/separation-of-concerns-react-container-and-presentational-components/

[Back to top](#)