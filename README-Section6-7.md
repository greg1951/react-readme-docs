1. [Interactive Components](#interactive-components)
   1. [Install the Chrome React Developer Tools Extension](#install-the-chrome-react-developer-tools-extension)
   2. [Project Exercise Setup](#project-exercise-setup)
      1. [Enhancing the App UI](#enhancing-the-app-ui)
      2. [Summary of the above changes](#summary-of-the-above-changes)
   3. [Handling Events the React Way](#handling-events-the-react-way)
   4. [What is State in React?](#what-is-state-in-react)
      1. [The useState Option](#the-usestate-option)
      2. [State Variable Should Not be Declared as Mutable](#state-variable-should-not-be-declared-as-mutable)
      3. [The Mechanics of State](#the-mechanics-of-state)
      4. [Updating Current State Based on Current State](#updating-current-state-based-on-current-state)
   5. [State Practical Guidelines](#state-practical-guidelines)
2. [Far Away Application](#far-away-application)
   1. [Project Creation Steps](#project-creation-steps)
      1. [Style the Components](#style-the-components)
      2. [Interactive Project Development](#interactive-project-development)
   2. [Controlled Elements](#controlled-elements)
3. [Flashcard App](#flashcard-app)
   1. [App Preface](#app-preface)
   2. [App Solution](#app-solution)
4. [Day Stepper App](#day-stepper-app)
5. [Section 7: Focus on State Management](#section-7-focus-on-state-management)
   1. [The Thinking in React Process](#the-thinking-in-react-process)
   2. [Local v. Global State](#local-v-global-state)
   3. [When and Where to Use State](#when-and-where-to-use-state)
   4. [Lifting State Up](#lifting-state-up)
      1. [Adding Items to Packing List](#adding-items-to-packing-list)
      2. [Deleting Items from PackingList Component](#deleting-items-from-packinglist-component)
   5. [Derived State](#derived-state)
   6. [Conditional Rendering Example](#conditional-rendering-example)
   7. [Sort Example](#sort-example)
   8. [One File Per Component](#one-file-per-component)

# Interactive Components

**Section Summary**: How to handle events, use state to render the UI and how to build forms the React way.

## Install the Chrome React Developer Tools Extension

This is a **Chrome extension**, not VS Code. [Click here to add to your Chrome browser](https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)

In the Console tab of the Developer Tools you can find the **Components** view. Click it and then move it to a more convenient location in the tabs. It will show all of the components on the page, their props and state. *You can also manipulate the state variables to see the re-rendering*, as shown below.

![Chrome React Developer Tools](readme-screenshots/chrome-react-dev-tools.png)

**MAIN TAKEAWAY**: However, once a page has many components on it, then the **Component tree view** in this extension will be most worthwhile, as a way to locate the state of one of the components.

## Project Exercise Setup

The application will consist of three steps with *Previous* and *Next* buttons to navigate to various forms.

1. Run command to create a project named *04-steps*: **npx create-react-app@5 04-steps** 
   
   *Remember*: The **@5** syntax ties the project version to React V5, so it relates with course material.

2. In VS Code select the **Close Folder** file menu option. 
3. Drag the **04-steps** project into VS Code.
4. In the new project, *remove everything* in the src folder, **except** **App.js** and **index.js**.
5. In the Terminal run: **npx start** command. 
6. When the browser comes up there will be errors, mostly complaining about deleted files.
7. Copy in the course 04-steps **Starter html and css files** into the *src* directory.
8. Replace the *App.js* with the following.

   ```javascript
      export default function App() {
      return (
         <div className="steps">
            <div className="numbers">
               <div>1</div>
               <div>2</div>
               <div>3</div>
            </div>
            <p className="message">Hello</p>
            <div className="buttons">
               <button>Previous</button>
               <button>Next</button>
            </div>

         </div>
      )};
   ```
9. After saving the form should render without any errors
10. *Optional*: Install the **Color Highlight** extension to highlight color properties in the style elements.

### Enhancing the App UI

Here some CSS and inline styles that were added to enhance the **static** implementation of the App UI. 

```javascript
const messages = [
  "Learn React ⚛️",
  "Apply for jobs 💼",
  "Invest your new income 🤑",
];

export default function App() {
  const step = 1;
  return (
    <div className="steps">
      <div className="numbers">
        <div className={`${step >= 1 ? "active" : ""}`}>1</div>
        <div className={`${step >= 2 ? "active" : ""}`}>2</div>
        <div className={`${step >= 3 ? "active" : ""}`}>3</div>
      </div>

      <p className="message">Step {step}: {messages[step - 1]}</p>

      <div className="buttons">
        <button style={{backgroundColor: "#7950f2", color: "#fff"}}>Previous</button>
        <button style={{backgroundColor: "#7950f2", color: "#fff"}}>Next</button>
      </div>

    </div>
  );
};
```

### Summary of the above changes

1. Each of the numbers in the display were updated to implement the **active** CSS class depending on the **step** number value.
   
2. The message paragraph was updated to display a step description based on the same **step** number value.

3. The buttons used **inline styles** (wrapped in javascript) to provide some color.

Now all that is needed is to **add functionality** to the **Previous** and **Next** buttons to change the step number.

## Handling Events the React Way

The element that needs the event listener will reference a Javascript function to handle it directly, as shown below in the button **onClick()** events.

```javascript
      <div className="buttons">
        <button style={{backgroundColor: "#7950f2", color: "#fff"}} 
        onClick={() => alert("Previous")}>Previous</button>     
        <button style={{backgroundColor: "#7950f2", color: "#fff"}} 
        onClick={() => alert("Next")}>Next</button>
      </div>
```

The onClick events should be referencing event handler functions. By convention those functions should be prepended with handle, as shown below.

```javascript
  function handlePrevious() {
    alert("Previous");
  }
  function handleNext() {
    alert("Next");
  }

  return (
      ...
      <div className="buttons">
        <button style={{backgroundColor: "#7950f2", color: "#fff"}} onClick={handlePrevious}>Previous</button>     
        <button style={{backgroundColor: "#7950f2", color: "#fff"}} onClick={handleNext}>Next</button>
      ...
```

**IMPORTANT**: Notice in the onClick events the **function itself is being passed as the argument**, e.g. ```handlePrevious``` and that it is not a *function call*, e.g. ```handlePrevious()```. When you specify a function call that function will be called immediately **when the page is rendered** and not on the desired event. 

## What is State in React?

**State is the most important concept to undertand in React**. It is important to understand:

1. What is state and why do we need it?
2. How to use state in practice?
3. How should we think about about state, when and where to use it and types of state.

Earlier we used Props as a way to communicate data between Parent and Child components. **State however, is data that a component can hold over time and that is needed throughout the app's lifecycle**. (Think of state as the *Component's memory* which is really a collection of state variables.)

When component state is updated, this will trigger React to re-render the component.

The term **User Interface** at this point needs to be clarified because all of the components comprise views in the UI. **Each component then is a view** in the application's user interface. 

1. State allows developers to update the *component's view*, by re-rendering it.
2. Allows for local variables to be persisted between renders.

### The useState Option

When useState is added there is an import from React added to the component. The useState function returns an array consisting of the name of the variable and a function to update it. 

The best way to implement this is with an **array destructor** as shown below.

```javascript
import { useState } from "react";
...
export default function App() {
  const [step, setStep] = useState(1);

  function handlePrevious() {
    setStep(step - 1);
  }
  function handleNext() {
    setStep(step + 1);
  }
```

Obviously the use of the **setStep state function** in the event handlers is simplistic but it work to change the value of step.

The *useState function* **is called a "hook"** in React. Hook function begin with a **use** prefix, e.g. *useEffect* and *useReducer*.

**GOOD TO KNOW**: Hooks can only be called at the top level of a function. You can't use a hook inside a function or a conditional, as the errors will be thrown.

### State Variable Should Not be Declared as Mutable

In the previous example if instead of the const declaration of the step variable we used let instead, then in the code, *if you bypass using the setStep method* and instead replaced it with ``` step = step + 1; ``` the **step state variable will not be updated**! React has no way of knowing about the state change.

**RULE**: Always treat state as immutable and use the hook method to update it.

### The Mechanics of State

When the state underlying a component view changes, React will re-render the component, essentially removing it and calling the component again. However, state is preserved during re-rendering, in order for the view to be updated.

![View Update Steps](readme-screenshots/component-view-re-rendered.png)

### Updating Current State Based on Current State

What is meant in this section, is that when current state is updated multiple times in the same event handler, the subsequent update does not get the previous updated value. Take the following example, where the setStep method was implemented for a state variable, the 2nd setStep would not recognize

```javascript
  function handleNext() {
    if (step < 3) {
      console.log('step 1: ' +step);
      setStep(step + 1);
      console.log('step 2: ' +step);
      setStep(step + 1);
    }
  }
```

The output of the above would be 

```sh
step 1: 1
step 2: 1
```

If this 2nd update needs to be done, then the only way for it to work is to declare a callback function in the hook setStep method, as shown below.

```javascript
  function handleNext() {
    if (step < 3) {
      console.log('step 1: ' +step);
      setStep((s) => s + 1);
      console.log('step 2: ' +step);
      setStep((s) => s + 1);
    }
  }
```

Interestingly the ```console.logs``` still showed the value of the **step** state variable to still be "1". This makes sense because the state change was not recorded until after the event completes.

**TIP**: Use the *Components view* (tab) in the **Chrome React Developer Tools extension** to confirm the step variable state change to 3. 

## State Practical Guidelines

* Use a state variable for **any data the component should track (or remember) over time**.
* When you want something in a component to be dynamic, create a piece of state related to it and update it when that component should be rendered.
* State changes usually occur in an event handler.
* For data that should not trigger rendering, don't use state.

# Far Away Application

The next sets of notes are based on the construction of the **Far Away application** that will implement a number of interactive React components, as shown below.

![Far Away App](readme-screenshots/section6-faraway-app-components.png)

## Project Creation Steps

The steps below outline how to ramp up a working project, step-by-step, to get a **static** React prototype working.

1. Closed the workspace folder in the VS Code.
2. In a terminal window, inside the projects directory, used the **npx create-react-app@5 05-travel-list** command.
3. After the above create ran, opened the *projects/05-travel-list* folder.
4. Removed extraneous files (for this point in the course) in the src directory of the new project.
5. Inspected the *src/index.js* to remove the reportWebVitals artifacts (not used in the course).
6. Replaced the *src/index.css* file for this application.
7. Rewrote the src/App.js to implement the empty components, as outlined above, using static content to simply get the layout working, without any CSS.

### Style the Components

1. Added the CSS classes to the various HTML elements in each of the components.
2.  Add *form fields, buttons and CSS classes* to the components, without implementing any of the event logic required.

The App.js static content implemented above is provided below.

```javascript
const initialItems = [
  { id: 1, description: "Passports", quantity: 2, packed: false },
  { id: 2, description: "Socks", quantity: 12, packed: false },
  { id: 3, description: "Charger", quantity: 1, packed: true },
];


export default function App() {
  return (
    <div className="app">
      <Logo />
      <Form />
      <PackingList />
      <Stats />
    </div>
  )
}

function Logo() {
  return <h1>🌴Far Away 💼</h1>;
}
function Form() {
  return (
  <form className="add-form">
    <h3>What do you need for your trip? 😍</h3>
    <select>
      {Array.from({length: 20}, (_, i) => i+1)
        .map((num) => (
          <option value={num} key={"num"}>{num}</option>
        ))};
    </select>
    <input type="text" placeholder="Item..."></input>
    <button>Add</button>
  </form>
  );
}
function PackingList() {
  return (
    <div className="list">
      <ul>
        {initialItems.map(item=><Item item={item} />)}
      </ul>
    </div>
  );
}
function Item({item}) {
  const {id, description, quantity, packed}={...item};
  // console.log(description);
  return (
      <li className="list">
        <span style={item.packed ? {textDecoration: "line-through"} : {}}>{quantity} {description}
        <button>❌</button></span>
      </li>
  );
}
function Stats() {
  return (
  <footer className="stats">
    <em>🧳You have X items in your list, and you have already packed Y%</em>
  </footer>
);}
```
### Interactive Project Development

In this section the components will be changed to make them interactive, implementing various event handlers.  

In the Form component the **form** will be changed to reference the **handleSubmit()** function, as shown below.

```javascript
function Form() {

  function handleSubmit(e) {
    e.preventDefault();
    alert("Add an item logic still to be done...")
  }

  return (
  <form className="add-form" onSubmit={handleSubmit}>
  ...
```

**IMPORTANT**: In the onSubmit event, simply **reference** the event, e.g. ```onSubmit={handleSubmit}```, but **do not call** it, e.g. ```onSubmit={handleSubmit()}```, as this is what React does. If you call the event handler, it will be called first thing when the page is opened and each time it is rendered, **and not just on the form submission**.

The **handleSubmit** event takes an event object argument you use to run the **preventDefault** method which will prevent the form from re-rendering. 

An **onClick event** could have been defined on the form button but by using a form **onSubmit** instead, pressing **enter** would also submit the form which is a desirable behavior.

## Controlled Elements

Inside the event object there is a **target** object that contains the data used in the form submission. However, this data is being held inside the DOM as **uncontrolled elements**. Optimally the form data should be available for use directly by React, as **controlled elements**.

* **Controlled components** in React are the components whose state and behaviors are managed by React components using states. 
* **Uncontrolled components** manage their own state and control their behaviors with the help of DOM.

  [For more information on this topic see this link](https://www.geeksforgeeks.org/controlled-vs-uncontrolled-components-in-reactjs/)

You may have guessed already, the **useState hook** is the means to implement controlled elements. Provided below is the **Form** component, implemented using controlled elements.

```javascript
function Form() {
  const [description, setDescription] = useState("");
  const [quantity, setQuantity] = useState(1);
  function handleSubmit(e) {
    e.preventDefault();
    alert("Add an item logic still to be done...")
  }

  return (
  <form className="add-form" onSubmit={handleSubmit}>
    <h3>What do you need for your trip? 😍</h3>
    <select onChange={(e) => setQuantity(Number(e.target.value))}>
      {Array.from({length: 20}, (_, i) => i+1)
        .map((num) => (
          <option value={num} key={"num"}>{num}</option>
        ))};
    </select>
    <input type="text" 
           placeholder={"Enter packing description..."} 
           onChange={(e) => setDescription(e.target.value)}>
    </input>
    <button>Add</button>
  </form>
  );
}
```

**NOTES**:
1. Two state variables for **quantity** and **description** are declared. 

2. The **onChange events** for both form fields utilize the **set methods** and extract the changed values from the form fields from the *target* object.

# Flashcard App

This was an intereting exercise in that although there was but one component, state and event handling was quite interesting. The solution is in two sections below, one of the top part of the App component and the second containing the actual implementation.

## App Preface

```javascript
import { useState } from "react";
import "./styles.css";

export default function App() {
  return (
    <div className="App">
      <FlashCards />
    </div>
  );
}

const questions = [
  {
    id: 3457,
    question: "What language is React based on?",
    answer: "JavaScript",
  },
  {
    id: 7336,
    question: "What are the building blocks of React apps?",
    answer: "Components",
  },
  {
    id: 8832,
    question: "What's the name of the syntax we use to describe a UI in React?",
    answer: "JSX",
  },
  {
    id: 1297,
    question: "How to pass data from parent to child components?",
    answer: "Props",
  },
  {
    id: 9103,
    question: "How to give components memory?",
    answer: "useState hook",
  },
  {
    id: 2002,
    question:
      "What do we call an input element that is completely synchronised with state?",
    answer: "Controlled element",
  },
];
```
## App Solution

```javascript
function FlashCards() {
  const [questionId, setQuestionId] = useState(null);

  function handleClick(id) {
    setQuestionId(id !== questionId ? id : null);
  }

  return (
    <div className="flashcards">
      {questions.map((card) => (
        <div
          key={card.id}
          className={card.id === questionId ? "selected" : ""}
          onClick={() => handleClick(card.id)}
        >
          <p>{questionId === card.id ? card.answer : card.question}</p>
        </div>
      ))}
    </div>
  );
}
```
**NOTES**:
1. To build the solution, first **iterate over the questions** and apply the necessary styling to reveal a grid of flashcards questions. 

2. Next, determine what state is needed in the App. It boils down to identifying when a flashcard is selected. A **flashcard question** has an ID associated with it so that is the only state that is required to reveal a **flashcard answer**.

  **GOOD TO KNOW**: There are three steps to implement state in React: (1)Define the state variable useState hook, (2)Reference the state variable in a component, (3)Update the state somewhere.

3. Updating the questionId state was the real challenge as the onClick event needs to know what questionId was selected, so the state variable can be updated.

  ```javascript
    function handleClick(id) {
      if (questionId === id) setQuestionId(null);
      else setQuestionId(id);
    }
  ```

4. The challenge was how to pass the questionId to the onClick event. It meant passing the event handler itself as an **anonymous function** in onClick: ```onClick={() => handleClick(card.id)}```

The above function can be simplified even more by the use of a **ternary conditional**, in lieu of the **if/else**. The simplified conditional is shown below.

```javascript
  function handleClick(id) {
    setQuestionId(id !== questionId? id : null);
  }
```
# Day Stepper App

This was an upgrade to an earlier exercise, but to introduce a slider input type and how to reset a form, as shown below.

![Day Stepper Form](readme-screenshots/day-stepper-app.png)

The Reset button is only visible when the Step or Count states have changed.

```javascript
import { useState } from "react";
import "./styles.css";

export default function App() {
  return (
    <div className="App">
      <Counter />
    </div>
  );
}

function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);

  const date = new Date("june 21 2027");
  date.setDate(date.getDate() + count);

  function handleReset(e) {
    setCount(0);
    setStep(1);
  }
  return (
    <div>
      <div>
        <span>
          <input
            type="range"
            min="1"
            max="10"
            value={step}
            onChange={(e) => setStep(Number(e.target.value))}
          />
          {`Step: ${step}`}
        </span>
      </div>

      <div>
        <button onClick={() => setCount((c) => c - step)}>-</button>
        <input
          type="text"
          value={count}
          onChange={(e) => setCount(Number(e.target.value))}
        />
        <button onClick={() => setCount((c) => c + step)}>+</button>
      </div>
      {count !== 0 || step !== 1 ? (
        <div>
          <input type="reset" value="Reset" onClick={handleReset} />
        </div>
      ) : null}

      <p>
        <span>
          {count === 0
            ? "Today is "
            : count > 0
            ? `${count} days from today is `
            : `${Math.abs(count)} days ago was `}
        </span>
        <span>{date.toDateString()}</span>
      </p>
    </div>
  );
}
```

# Section 7: Focus on State Management

The following are the main areas of focus in this section of the course.
1. Thinking in React: new mindset required for React apps
2. State management: when and where to create state
3. Derived state
4. Lifting up state: child to parent communication

## The Thinking in React Process
It is a process that consits of the following steps.
1. Break the desired UI into component and establish a component tree.
2. Build a static version in React (without state)
3. Decide on the app's state
   * When to use it (i.e. what state do I need to implement interactivity?)
   * Types of state (local v global)
   * Where to place each piece of state
4. Establish data flow
   * One-way data flow
   * Child-Parent communication
   * Accessing global state

Steps 3 & 4 comprise **State Management**.

## Local v. Global State

* Local state is defined as state needed in one or a *few* components. 
  * It is **state created in a component using the useState** hook. 
  * It is state that is passed to other child components using **props**.
  * We should always start with local state and only to move to global if necessary
  
* Global state is state used by all components in the app.
  * Think of it as shared state. It matters more in large applications.

## When and Where to Use State

This flowchart nicely summarizes when to create state and then where it belongs in the app.

![When and Where](readme-screenshots/state-when-and-where.png)

## Lifting State Up

**SCENARIO**: In the Far Away app the **Form** component is responsible for creating a packing item but it is the **PackingList** component that is responsible for adding that new item to its list of items. Conversely, when an item is to be removed from the **PackingList**, it is the **Item** component that triggers an event to remove it. 

Lifting the state up means *making not only the state available to the various involved components but also any events that must be shared*. Looking at the bottom of the above flowchart, the state must be lifed up to the **first common parent**, in this case this is **App**.

Shown below are snippets of the code to implement the add items to the PackingList

### Adding Items to Packing List
1. The **items** state variable and the **handleAddItems** event must be lifted up to the **App** parent component.

2. The **Form** component must be passed a *prop* of the **handleAddItems** so that component can call the event.

3. The **PackingList** component must be passed *props* for the **items** state variable as well as the **handleDeleteItem** event.

```javascript
export default function App() {
  const [items, setItems] = useState([]);

  function handleAddItems(item) {
    setItems((items) => [...items, item]);
  }

  function handleDeleteItem(id) {
    setItems((items)=>items.filter(item=>item.id !== id))
  }
  ...
  return (
    <div className="app">
      <Logo />
      <Form onAddItems={handleAddItems} />
      <PackingList items={items} onDeleteItems={handleDeleteItem}/>
      <Stats />
    </div>
  ...
function Form({onAddItems}) {  

  const [description, setDescription] = useState("");
  const [quantity, setQuantity] = useState(1);

  function handleReset(e) {
    setDescription("");
    setQuantity(1);
  }
  ...
  function handleSubmit(e) {
    e.preventDefault();

    if (!description) return;

    const newItem = {description, quantity, packed: false, id: Date.now()};
    onAddItems(newItem);
    ...
```

### Deleting Items from PackingList Component
1. The **onDeleteItems** *prop* was passed from **App** to **PackingList** so that child component can pass a *prop* to its **Item** child component to perform the delete.

2. The **Item** component then defines an *onClick* event on a delete button that contains an anonymous function to call the **onDeleteItems** event, passing the *item.id* to be removed.

```javascript
function PackingList({items, onDeleteItems}) {
  return (
    <div className="list">
      <ul>
        {items.map((item) => (
          <Item item={item} key={item.id} onDeleteItems={onDeleteItems}/>
        ))}
      </ul>
    </div>
  );
}
function Item({item, onDeleteItems}) {
  return (
      <li className="list">
        <span style={item.packed ? {textDecoration: "line-through"} : {}}>{item.quantity} {item.description}
        <button onClick={()=>onDeleteItems(item.id)}>❌</button></span>
      </li>
  );
}
```

## Derived State

*Derived state is state that is computed from existing state variables or props.*

Be cognizant of your state variables, ensuring you absolutely need them as they require they are synchronized. As shown below the numItems and totalPrice are unnecessary, as their values can be derived from other state.

![Derived State](readme-screenshots/derived-state.png)

The other issue is that if any of the state variables are updated re-rendering occurs. If there are three state variable, three re-renderings occur. Shown below is a better solution.

![Derivation](readme-screenshots/deriving-from-state.png)

## Conditional Rendering Example

The Stats component of the Far Away app was an example of derived state but it was also a pretty good example of conditional rendering in a component.

```javascript
function Stats({items}) {
  if (items.length === 0) 
    return (
      <footer className="stats">
        <em>You need to add items to your packing list! 🚀</em>
      </footer>
    );

  const numItems=items.length;
  const numPacked=items.filter((item) => item.packed).length;
  const pct = Number(Math.round((numPacked / numItems) * 100));
  console.log('# items: ' +numItems +'# packed: ' +numPacked + ', pct: ' +pct);

  if (pct===100)
    return (
      <footer className="stats">
        <em>You are all packed and ready to go! 🌴</em>
      </footer>
    );

  return (
  <footer className="stats">
    <em>🧳You have {numItems} items in your list, and you are {pct}% packed</em>
  </footer>
);}
```

## Sort Example

In the Far Away app, the rendering of the **PackingList** can be sorted three ways, as shown below.

![Sort PackingList](readme-screenshots/sort-packing-list-example.png)

1. The state for the sorting can be confined to the **PackingList** component, it does not need to be shared (or "lifted").

2. The **sort** state variable is set simply by an **onChange** event on the *select* input. 

3. The rendering of the **sortedItems** requires the original items to be **splice()**'d first (remember, the sort operation mutates the list, so copy it first), then sorted.

4. The **item.packed** property is a boolean. By converting it to a **Number** allows us to sort as either "0" (false) or "1" (true).

```javascript
function PackingList({items, onDeleteItems, onCheckedItems}) {
  const [sort, setSort]=useState("input");

  let sortedItems;

  if (sort === "input") 
    sortedItems=items;
  if (sort === "description") 
    sortedItems=items.slice().sort((a,b)=> a.description.localeCompare(b.description));
  if (sort === "packed")
    sortedItems=items.slice().sort((a,b) => (Number(a.packed)) - Number(b.packed));

  return (
    <div className="list">
      <ul>
        {sortedItems.map((item) => (
          <Item item={item} key={item.id} onDeleteItems={onDeleteItems} onCheckedItems={onCheckedItems}/>
        ))}
      </ul>
      <div>
        <select value={sort} onChange={e=>setSort(e.target.value)}>
          <option value="input">Sort by input order</option>
          <option value="description">Sort by description</option>
          <option value="packed">Sort by packed status</option>
        </select>
    </div>

    </div>
  );
}
```

## One File Per Component

Even in this smaller React Far Away project, the main App.js file was getting rather lengthy, entailing a fair amount of scrolling to make changes. Best practices would have you isolate each component into it's own **.js** file and all of the components in a **src/components** folder.

1. Create a **src/components** folder.
2. Move the **App.js file into the components folder**.
3. Update the **src/index.js** to reference App in the components folder.
4. In the **App.js file**, contract all lines for component, right-click, **select Refactor** option and then select the **Move to a new file** option.
5. Add the **default** keyword in the newly moved .js file to the export at the top of the file.
6. Remove the **curly braces** from the import statement in App.js file.

**NOTE**: If a component has a child component inside of it, then the move to a new file option will leave that component in the newly moved file with an export defined for the child. To be consistent, export that child to another file.

Below is what the App.js file contains after moving all of the components into their own .js files. **What remains in *App.js* are the *shared* state variables and event handlers used by the child components**. Now, instead of scrolling around to locate a components just open the components from the components folder.

```javascript
import { useState } from "react";
import Logo from "./Logo";
import Form from "./Form";
import PackingList from "./PackingList";
import Stats from "./Stats";

export default function App() {
  const [items, setItems] = useState([]);

  function handleAddItems(item) {
    setItems((items) => [...items, item]);
  }

  function handleDeleteItem(id) {
    setItems((items)=>items.filter(item=>item.id !== id))
  }

  function handleCheckedItem(id) {
    setItems((items)=>items.map((item)=>
      item.id === id ?{...item, packed: !item.packed} : item
      )
    );
  }

  function handleClearItems() {
    const confirmed = window.confirm("Are you sure you want to remove all packing items?");
    if (confirmed) setItems([]);
  }

  return (
    <div className="app">
      <Logo />
      <Form onAddItems={handleAddItems} />
      <PackingList items={items} onDeleteItems={handleDeleteItem} onCheckedItems={handleCheckedItem} onClearItems={handleClearItems}/>
      <Stats items={items} />
    </div>
  )
}
```

