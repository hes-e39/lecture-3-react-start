# React Application Structure

We will be using Vite to create our applications. This template includes a few tools that were discussed in lectures.

The template has the following contents:

```
/.git
/.github/workflows
.gitignore
/.vscode
biome.json
index.html
/node_modules
package-lock.json
package.json
postcss.config.js
/public
README.md
/src
tailwind.config.js
tsconfig.json
tsconfig.node.json
vite.config.ts
```

*Git related*

**.github/workflows** stores Github Action definitions that will deploy your app when you push changes to the main branch

**.git** stores all information required to keep your project under version control (commits, pushes, repositories, etc)

**.gitignore** pre-filled list of files that should not be included in version control

*Dependencies*

**node_modules** this folder stores all packages and modules installed in this application

**package.json** metadata about our application, including:

- **name**: the name of our app - be careful with naming conventions, no uppercase letters and should be a URL-safe string
- **version**
- **private**: if set to **true**, **npm** will refuse to publish this app
- **dependencies**: a list of all required modules to run this app
  - `@testing*` packages used to run tests
  - `react`
  - `react-dom` serves as a link between React and the HTML DOM
  - `react-router-dom` router to navigate through the application (used lated during the course)

> Note about versioning - in the **package.json** file you will find versions defined as `^1.2.3`. This does not mean specifically `v1.2.3` but actually translates to "compatible with v1.2.3" or "the latest minor or patch version" according to **semver**. This means you could really be installing `v1.3.0`. Unfortunately, module developers are not always careful in versioning and sometimes might introduce a breaking change in a minor or patch version.

- **scripts**
  - `dev`: starts the app in development mode (hot reload, error logging, etc)
  - `build`: generates the bundle and static assets into the **build** folder, in production mode with optimized performance
  - the others can be ignored

**yarn.lock** (or **package-lock.json**) stores the exact dependency versions for your project. Because in our **package.json** we can be vague with versions, developers running `npm install` can get different versions, hence creating inconsistencies. The lock files solve that by being specific about what versions are installed. More info here: https://docs.npmjs.com/cli/v7/configuring-npm/package-lock-json

*Configuration*
We have added some additional modules and configuration files so that we have some consistency among projects and an overall consistent developer experience. These are not mandatory but recommended and include: TypeScript, BiomeJS, PostCSS and Tailwind.

*React related*

- `index.html` this is the entry point to our application. When we make a request to view the app, the response will be the `index.html` file. Note that this is a traditional HTML file, no JSX, no React - the important pieces here are:

```html
<!-- The root node to which our entire application will be appended -->
<div id="root"></div>
```

**src** stores all of the source code in our app, including components, styling, etc

## YARN vs NPM
- performance: yarn
- security: similar - both check packages with known vulnerabilities

# Hello World

Like every first interaction with any coding language or framework, we'll start with our "Hello World" example. First, replace the contents of `src/index.js` with the following:

```jsx
import ReactDOM from 'react-dom';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<div>Hello World</div>);
```

Now run the start command `npm start` and now your application should be running at http://localhost:3000. Note that if there is something else running on port 3000, the app will ask if you want to run on a different port.

# JSX

The first thing you will notice is this strange mix of HTML and JavaScript. The reality is this is not vanilla JS, but JSX, which is a syntax extension to JavaScript. You can think of it as a templating language within JS. Of course browsers don't understand JSX, so behind the scenes React takes care of transpiling all our JSX into vanilla JS as follows:

```jsx
// JSX
const comp = <div>Hello World</div>;

// is transpiled into
const comp = React.createElement('div', {}, 'Hello World');
```

We could avoid the transpiling if we used React's top-level API but it's rather tedious as structure grows so we'll stick to JSX.

In the last line we added, `ReactDOM` takes care of appending our component to the `#root` component of the HTML document, described above in the `index.html` file.

### Expressions

Since JSX is still JavaScript, we can include code inside it. For example, we can use variables as follows:

```jsx
const variable = 'World';
const comp = <div>Hello {variable}</div>; // will render 'Hello World'
```

Or even call functions:

```jsx
const printAnimal = ({ name, action }) => {
  return `the ${name} ${action}`;
};

const animal = {
  name: 'dog',
  action: 'barks',
};

const comp = <div>{printAnimal(animal)}</div>; // will render 'The dog barks'
```

When content spans multiple lines, you can include parentheses:

```jsx
const comp = (
  <div>
    <div>
      <b>Hello</b> {printAnimal(animal)}
    </div>
  </div>
);
```

In the examples above, we rendered a string into a JSX expression, but we can also render other JSX expressions:

```jsx
const printAnimal = animal => {
  if (!animal) {
    return (
      <div>
        <b>Inexistent animal</b>
      </div>
    );
  }

  const { name, action } = animal;
  
  return (
    <div>
      the <b>{name}</b> {action}
    </div>
  );
};

const animal = {
  name: 'dog',
  action: 'barks',
};

const comp1 = <div>{printAnimal(animal)}</div>; // will render 'The <b>dog</b> barks'
const comp2 = <div>{printAnimal(animal)}</div>; // will render '<b>Inexistent animal</b>'
```

# React Components

Components are at the heart of React. It's the equivalent of the arrival of objects to object-oriented programming, giving us the ability to encapsulate business logic, structure, styling in a single reusable unit.

## Are React Components Web Components?

In short, no. In long, no. When HTML started we had a very limited set of tags (divs, spans, etc). As the web grew, we started needing more complex elements like tabls, inputs, video. Developers started to combine all these tags to create even more complex custom elements using JS and CSS. However, in the browser, these rendered as standard elements, which could potentially class with the overall document. For example, a selector from the main document could target a part of the custom component and modify its behavior. Web Components provides an API to avoid this, by encapsulating the component entirely and preventing conflics with the parent document. Previously you could find this type of behavior in iframes. This also provides reusability as the definition of the component is done once but they can be reused as many times as needed in the document.

React Components are NOT web components. In the browser, React components end up rendering with the tags used in its definition (divs, spans, even web components). This means we do not have any type of encapsulation of our React components. This is, nothing prevents a script from using a selector that targets a part of our React component and modifying it. There are ways to reduce the probability of this problem but not to totally prevent it. However, React components CAN be reused and this is highly encouraged like objects are in OOP.

In older versions of React, we would create our own components using a class that extends from `React.Component`:

```jsx
// All React componens require a render method that return a valid React element
class Animal extends React.Component {
  render() {
    return <div>Hello, I'm an animal!</div>
  }
}

// We can then use our component as an additional HTML tag:
<Animal/>
```

However, React v16.8.0 introduced a new and more concise way of writing components as simple functions. Since JS is mostly built around functions it was an easy transition, albeit somewhat confusing since some niceties of classes were lost (more about this later).

```jsx
// The equivalent functional component. It's a simple function which returns a valid React element:
const Animal = () => {
  return <div>Hello, I'm an animal!</div>
}
```

### Rules for Components

1. Every component should be a function that returns a renderable node (or simply return if using a functional component). This can be a string, an array, a number, a JSX expression. If you don't want to render anything, you are required to return `null` - returning `undefined` will result in an exception.
2. All components should use title case names. Use `MyComponent` instead of `myComponent`, `mycomponent` or `my_component`.
3. If returning a JSX expression, it can only have a single parent:

```jsx
// Valid
return (
  <div>
		<span>I am valid 1</span>
    <span>I am valid 2</span>
	</div>
);

// Invalid
return (
  <div>
  	<span>I am not valid 1</span>
	</div>
	<div>
    <span>I am not valid 2</span>
	</div>
);
```

### Component Properties (props)

Props are to components what arguments are to functions. They are inputs that parent components will pass in, but that cannot be modified by the component itself. These can include any valid JavaScript value, like numbers, strings, functions or even other components. Props can be added the same way that we add attributes to HTML tags as follows:

```jsx
// Strings, numbers, booleans
<div prop1="123" prop2={123} prop3={true}>hello</div>;

// Functions and other components
<div
  prop4={() => {
    console.log('Hi there!');
  }}
  prop5={<div>Component Prop</div>}
/>
// Note the self-closing tag here, which is not valid HTML but is valid JSX
```

Components receive props as an instance field called `props`, which can be accessed as follows

```jsx
const Animal = props => {
  // This syntax is not very common as props are usually destructured in the function signature directly
  const { name, action } = props;
  
  // Note how we are returning null if we don't have props that are used in the expression below
  if (!name || !action) {
    return null;
  }
  
  return <div>Hello, I'm an animal. My name is {name} and I {action}</div>
}

// We can use this component as
<Animal name="dog" action="barks"/>
<Animal name="cat" action="meows"/>
```

### Event Handling

In standard HTML we can listen to events on nodes by attaching a handler:

```html
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>;
```

In React, we can add handlers to any event on a component, including **blur**, **click**, **mouseEnter**, etc, simply following the camel case naming convention `on<Event>`. For example, `onClick`, `onMouseEnter`, `onBlur` , etc.

```jsx
const App = () => {
  return (
    <button
      onClick={e => {
        console.log('The button was clicked.');
      }}
      onMouseEnter={() => {
        console.log('Hovering button');
      }}
      onMouseLeave={() => {
        console.log('Not hovering button anymore');
      }}
    >
      Click me
    </button>
  );
}
```

### Using `this`

In class components `props` and `state` were bound to `this` and it required special bindings when used in internal functions of the class. This caused a lot of bugs and additional code needed for simple things. We used to dedicate some time to `this` but we're not covering classes anymore so we'll skip it.

### Components as Children

Parent-child hierarchy is a crucial concept in HTML and in any tree structure. React components can have children too and they can be accessed through a special `prop` called `children` that every component receives. Below we can see two examples, `PropBook` receives it's content through it's `content` prop, while `ChildBook` uses `children` instead:

```jsx
const PropBook = ({ title, content }) => {
  return (
    <div>
      <h1>{title}</h1>
      {content}
    </div>
  );
};

const ChildBook = ({ title, children }) => {
  // All components receive children - if there are none, for example in a self-closing tag, it will be null

  return (
    <div>
      <h1>{title}</h1>
      {children}
    </div>
  );
};

const App = () => {
  const content = (
    <div>
      <p>hello 1</p>
      <p>hello 2</p>
    </div>
  );

  return (
    <div>
      {/* Passing the content in a standard prop */}
      <PropBook title="Hello World" content={content} />

      {/* Now we nest the content inside the book, which can then be accessed through its children prop instead */}
      <ChildBook title="Hello World">{content}</ChildBook>

      {/* We can also set the children prop explicitly */}
      <ChildBook title="Hello World" children={content} />
    </div>
  );
};
```

### Fragments

As we know, we can only return a single parent from a component. This can result in a proliferation of unnecessary wrapping tags, generally `<div>`s. To avoid this, React introduced Fragments, which are essentially ghost tags. They serve as a wrapping element but do not render to the DOM.

```jsx
// To return a single parent, we can wrap in a div:
return <div>
	<span>Child 1</span>
  <span>Child 2</span>
</div>

// Or use Fragments - this will result in two spans, but no wrapping element in the DOM:
return <React.Fragment>
	<span>Child 1</span>
  <span>Child 2</span>
</React.Fragment>

// We can also use the convenient equivalent syntax:
return <>
	<span>Child 1</span>
  <span>Child 2</span>
</>
```

### Conditional Rendering

Very often an application needs to render data and nodes based on some condition defined in the business logic. A common example is rendering the current user if logged in, or a "Log In" button otherwise. We could do this as follows:

```jsx
const User = ({ user }) => {
  if (user) {
    return <div>Welcome {user.username}</div>;
  }
  
  return <button>Log In</button>;
}
```

or using an inline conditional statement:

```jsx
const User = ({ user }) => {
  return user ? <div>Welcome {user.username}</div> : <button>Log In</button>;
}
```

React also allows the following syntax.

```jsx
const User = ({ user }) => {
  return <div>
    {user && <div>Welcome {user.username}</div>
    {!user && <button>Log In</button>}
  </div>
}
```

In JavaScript, `boolean statement && expression` returns:

- if the `boolean statement` is truthy, return `expression`
- if the  `boolean statement` is falsy, return `boolean statement`

Note that in the second case, if the `boolean statement` is a renderable node, it will be rendered, which could lead to unexpected results. For example:

```jsx
const Customer = ({ name, orderCount }) => {
  // We only want to render Customers that have orders  
  // If orderCount is 0, it will be rendered as 0 is renderable
  return <div>
    {orderCount && <div>{name} has {orderCount} orders</div>}
  </div>
}
```

### Iterables

We can render collections of elements by simply mapping over the items and returning a renderable node for each. However, the outermost node for each element in a list require an extra unique `key` prop on each item. This allows React to uniquely identify each item and update them efficiently when their state changes:

```jsx
const OrderList = ({ orders }) => {
  return orders.map(o => (
    <div key={o.id}>
      <div>{order.id}</div>
      <div>{order.product}</div>
    </div>
  ))
}
```

If a unique `id` is not available for each item, a common approach is to simply use their `index`:

```jsx
return orders.map((o, index) => (
  <div key={index}>
    <div>{index}</div>
    <div>{order.product}</div>
  </div>
))
```

Although this works, it could degrade performance when the order of the elements can change. For example, a simple insertion of an element at the beggining of the list would cause all keys to be updated and the entire list to be re-rendered even though only one element changed.

#### Rules for Keys

- they should be unique for each element
- avoid using indexes unless order is unchanged
- unlike `id` attributes in traditional HTML, keys need not be globally unique



