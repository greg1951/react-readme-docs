
1. [The Ultimate React Course - React Concepts](#the-ultimate-react-course---react-concepts)
   1. [Components](#components)
   2. [Component Hierarchies](#component-hierarchies)
   3. [What is JSX?](#what-is-jsx)
   4. [Babel](#babel)
   5. [Imperative Versus Declarative Approach](#imperative-versus-declarative-approach)
   6. [Simple Example](#simple-example)
   7. [Separation of Concerns in React Apps](#separation-of-concerns-in-react-apps)
   8. [React Props](#react-props)
      1. [The Props Object](#the-props-object)
      2. [Destructuring Props](#destructuring-props)
   9. [React Data](#react-data)
   10. [One-Way versus Two-Way Data Flow](#one-way-versus-two-way-data-flow)
   11. [JXS Rules](#jxs-rules)
       1. [General JSX Rules](#general-jsx-rules)
       2. [JSX and HTML Differences](#jsx-and-html-differences)
   12. [Rendering Lists](#rendering-lists)
   13. [Conditional Rendering](#conditional-rendering)
       1. [The \&\& Conditional Operator](#the--conditional-operator)
       2. [The Ternary Operator](#the-ternary-operator)
   14. [Conditional Rendering with Multiple Returns](#conditional-rendering-with-multiple-returns)
   15. [React Fragments](#react-fragments)


# The Ultimate React Course - React Concepts

* **Core Concepts**: compoments, props, JSX
* Creating and reusing components
* Rendering *lists*
* **Conditional** rendering
* Writing your own code

This section of the course will build the Pizza Menu project.


## Components
React apps are entirely made out of components, they are UI building blocks. React renders the view for each of the components in the app. Each component has its own data, logic and appearance. Components are generally **nested** inside other larger components.

## Component Hierarchies
A *component tree* identifies the components, and their hierarchy as **parent** and **child** components.

## What is JSX?

*JSX is a declarative syntax that describes what components **look like** and **how they work**.*

*JSX is an extension of JS that allows us to embed **JS, CSS and React components into HTML**.*

## Babel

**Browsers do not understand JSX.** Therefore, the JSX must be converted to a *React.createElement function call* if it is to be displayed in the browser. 

Babel is a tool included in the *node_modules* folder that does this conversion which the *Create-React-App* command provided.

## Imperative Versus Declarative Approach

In the **imperative approach** (e.g. HTML and JS) of writing UI, you specify the manual DOM elements and DOM traversals.

With the React **declarative approach**, JSX defines what the data looks like *based on the current data in the component*. A benefit of this is the DOM is never referenced, except through the JSX.

## Simple Example
*New components are written in React using **functions**.*

In the example below, the **Pizza component** is written and included in the returned browser page.

```javascript
import React from "react";
import ReactDOM from "react-dom/client";

function App() {
    return <div>
        <h1>Hello React!</h1>
        <Pizza />
    </div>
}

function Pizza() {
    return ( 
        <div>
            <h2>Pizza Spinaci</h2>
            <img src="pizzas/spinaci.jpg" alt="Pizza spinaci" />
            <p>Tomato, mozarella, spinach, and ricotta cheese</p>
        </div>
    )
};

const root = ReactDOM.createRoot(document.getElementById("root"));

root.render(
    <React.StrictMode>
        <App />
    </React.StrictMode>
);
```

**Notes**

1. Project setup: Static resources (e.g. the pizzas folder containing images) go into the **public** folder.
2. The **index.js** file goes into the **src** folder.
3. Never nest the function declaration inside another. Declare the component functions at the top level.
4. Running the component (e.g. see App component above) in **StrictMode** is a tip for debugging.in React.

**StrictMode** in React is a development tool for highlighting potential problems in an application. It activates additional checks and warnings for its descendants, helping to identify unsafe practices, deprecated APIs, and unexpected side effects. 

## Separation of Concerns in React Apps

What is meant by Separation of Concerns in computer science? It is the organization of code into clearly identifiable sections as a means to organize the code. The goal of SoC was to make the code more maintainable.

Before the advent of single page applications, the separation of concern was based upon **one technology per file**. That is, there would be a *file containing the HTML*, a separate *file containing the Javascript*, and another *file containing the CSS*, etc. As single page apps evolved, there was the **realization that logic and UI are tightly coupled**, and so, why keep them separated? That is in essence what JSX provides.

There is still a separation of concern in React components and that is that there is a **file for each component** in the single page app.

## React Props

A component can render data about one thing, and then be reused to render data about something else, using the components UI and logic to do so.

There are two basic steps to implement Props: 
1. Update the parent component to pass the props
2. Update the child component to implement the props.

The example below illustrates how the parent **Menu component** will use the **Pizza component** to display the pizzas as it iterates through that data.

```javascript
function Menu() {
  let pizzas = pizzaData;
  // pizzas = [];  // uncomment this to test the Still working on our menu message...
  const numPizzas = pizzas.length;

  return (
    <main className="menu">
      <h2>Our menu</h2>

      {numPizzas > 0 ? (
        <>
          <p>
            Authentic Italian cuisine. 6 creative dishes to choose from. All
            from our stone oven, all organic, all delicious.
          </p>

          <ul className="pizzas">
            {pizzas.map((pizza) => (
              <Pizza pizzaObj={pizza} key={pizza.name} />
            ))}
          </ul>
        </>
      ) : (
        <p>We're still working on our menu. Please come back later :)</p>
      )}

    </main>
  );
}
```
The Pizza component was modified to take the contents of the iterated pizza. 

```javascript
function Pizza({ pizzaObj }) {
  console.log(pizzaObj);

  // if (pizzaObj.soldOut) return null;

  return (
    <li className={`pizza ${pizzaObj.soldOut ? "sold-out" : ""}`}>
      <img src={pizzaObj.photoName} alt={pizzaObj.name} />
      <div>
        <h3>{pizzaObj.name}</h3>
        <p>{pizzaObj.ingredients}</p>

        <span>{pizzaObj.soldOut ? "SOLD OUT" : pizzaObj.price}</span>
      </div>
    </li>
  );
}
```
**Don't Forget!**: The child component argument **must be enclosed in curly braces**. As shown in the example above, the argument **pizzaObj** is ```function Pizza( {pizzaObj} )```

### The Props Object

In the previous example the child component arguments was defined using **pizzaObj**, as shown in the snippet below.

```javascript
function Pizza({ pizzaObj }) {
```

This can get quite tedious where there are a lot of child components. Instead, React will inject the argument into a **props** object. The child component must then refer to it as a qualifier, as shown in the example below.

```javascript 
console.log(props.pizzaObj.name); 
```

As a note, even if the child component is not be passed any properties, the **props object is simply empty** when the child component starts. The point is that React will automatically pass the props object to the child component. 

### Destructuring Props

This is an important learning point which is that the props need not be qualified with the props.pizzaObj qualifier in the name. Instead, **destructure the properties** contained within the object, utilizing the spread operator as well, as shown below.

```javascript
function Pizza(props) {
  console.log(props.pizzaObj);
  const {name, photoName, ingredients, price, soldOut} = {...props.pizzaObj};

  return (
    <li className={`pizza ${soldOut ? "sold-out" : ""}`}>
      <img src={photoName} alt={name} />
      <div>
        <h3>{name}</h3>
        <p>{ingredients}</p>

        <span>{soldOut ? "SOLD OUT" : price}</span>
      </div>
    </li>
  );
}
```


## React Data

The data a React component uses is either **Props** or **State**. Props are read-only data passed from the Parent to the Child components, which can only be updated by the Parent component. On the other hand State is data the logic that a Child component can change. 

**Question**: What if you find that you have data that needs to mutate, *should it be defined as a Prop or State?* If it needs to be changed in the Child component then it must be defined as a State. 

The principal rule in React is that **functions should have no side-effects**. This means that if you change an object (i.e. Prop) in a Child component, it would impact the parent component as a side-effect. 

## One-Way versus Two-Way Data Flow

React implements **one-way data flow**, rather than two-way data flow: *Props one-way **down**, State one-way **up***. This makes React applications more predicatable, and easier to debug. It also performs better. Angular on the other hand implements two-way data flow and not perform as well.

## JXS Rules

There are general rules that can be stated about JSX and then there are rules that are related to JSX differences with HTML.

### General JSX Rules
1. You can enter Javascript mode by entering **{ }** 
2. You can enter any **Javascript expression** inside the **{ }**
3. You cannot use any Javascript statements (e.g. if/else, for, switch, etc.) inside the **{ }**
4. JSX produces a Javascript expression, which means that JSX can be placed inside the **{ }**
5. JSX can only have one root element However, you can use a **React.Fragment** (or the shorter < > form) should you require more than one root element in the JSX.

### JSX and HTML Differences
1. Use of **className** instead of **HTML's class**
2. **htmlFor** instead of **HTML's for**
3. Every tag needs to be closed.
4. All event handlers need to be **camelCase**, e.g. *onClick* or *onMouseOver*.
5. CSS property names are **camelCase**.
6. Comments need to be in **{ }** because they are JS.

## Rendering Lists

One of the more common requirements in an application. As mentioned previously an array.forEach will not works as it is a JS statement and not an expression. Instead, **use the array map() method.**, as shown in the snippet below.

```javascript
  <ul className="pizzas">
    {pizzas.map((pizza) => (
      <Pizza pizzaObj={pizza} key={pizza.name} />
    ))}
  </ul>
```

## Conditional Rendering

Examining the use of the **&&, || and ternary conditionals** here.

### The && Conditional Operator
The rule about truesy applies here where the first operand is false, in which case the "div" renderting occurs.

```javascript
  {isOpen && (
    <div className="order">
      <p>
        We're open until {closeHour}:00. Come visit us or oder online.
      </p>
    </div>
  )}
```

### The Ternary Operator

If there are pizzas to be had then render them, otherwise display a message about the pizza not being available.

```javascript
function Menu() {
  let pizzas = pizzaData;
  const numPizzas = pizzas.length;

  return (
    <main className="menu">
      <h2>Our menu</h2>

      {numPizzas > 0 ? (
        <>
          <p>
            Authentic Italian cuisine. 6 creative dishes to choose from. All
            from our stone oven, all organic, all delicious.
          </p>

          <ul className="pizzas">
            {pizzas.map((pizza) => (
              <Pizza pizzaObj={pizza} key={pizza.name} />
            ))}
          </ul>
        </>
      ) : (
        <p>We're still working on our menu. Please come back later :)</p>
      )}

    </main>
  );
}
```

## Conditional Rendering with Multiple Returns

What this simply means is that although a JSX can only return one thing, what is returned can be based on a condition.

```javascript
function Pizza(props) {
  console.log(props.pizzaObj);

  if (pizzaObj.soldOut) return null;

```

The above simply would have removed the pizza from the menu altogether. A more elegant solution is to replace the price of the pizza with SOLD OUT, but keep it in the menu.

```javascript
  return (
    <li className={`pizza ${props.pizzaObj.soldOut ? "sold-out" : ""}`}>
      <img src={props.pizzaObj.photoName} alt={props.pizzaObj.name} />
      <div>
        <h3>{props.pizzaObj.name}</h3>
        <p>{props.pizzaObj.ingredients}</p>

        <span>{props.pizzaObj.soldOut ? "SOLD OUT" : props.pizzaObj.price}</span>
      </div>
    </li>
  );
}
```

The CSS for the sold out pizza was quite cleverly done.

```css
.pizza.sold-out img {
  filter: grayscale();
  opacity: 0.8;
}
```

**LEARNINING POINT**: Referencing the **props** object is just a needless misdirection. Simply reference the real argument(s) in the child component, within the **{ }** curly braces, of course. For example: 

```javascript
function App() {
  const openHour = 12;
  const closeHour = 22;
  return (
    <div className="container">
      <Header />
      <Menu />
      <Footer openHour={openHour} closeHour={closeHour} />
    </div>
  );
}
...
function Footer({openHour, closeHour}) {
  const hour = new Date().getHours();
  const isOpen = hour >= openHour && hour <= closeHour;
  console.log("isOpen? " +isOpen);
  ...
```

## React Fragments

As learned earlier, a fragment can only return a single parent. Wrapping inside a "div" block doesn't always render multiple parents properly. In lieu of a "div" block however, specify a "<>" block. The DOM elements returned for the HTML within the "<>" block will be separately rendered.

```javascript
      {numPizzas > 0 ? (
        <>
          <p>
            Authentic Italian cuisine. 6 creative dishes to choose from. All
            from our stone oven, all organic, all delicious.
          </p>

          <ul className="pizzas">
            {pizzas.map((pizza) => (
              <Pizza pizzaObj={pizza} key={pizza.name} />
            ))}
          </ul>
        </>
      ) : (
```

For clarity, instead of *<>* and *</>* you can write it in the long form:

```javascript
<React.Fragment key='menuFragment'> 
...
</React.Fragment>



