1. [Basic Wrapper Features](#basic-wrapper-features)
2. [Simple Approach](#simple-approach)
3. [Using React ComponentProps](#using-react-componentprops)
4. [Wrapper Button Component](#wrapper-button-component)
5. [Custom Form Component](#custom-form-component)

# Basic Wrapper Features

An `Input` component provides the ability to get user input anywhere in the app and is an important component used in the `Form` component also described here.

At a high-level, the markup below explains what it consists of (with some React bells and whistles, of course).

```javascript
export default function Input(props) {
    return (
            <label></label>
            <input />
        </p>
    );
}
```

# Simple Approach

Here the _HTML input element_ can have a number of attributes associated with it and the `InputProps` type must list them. You need to define the permitted values for them as well.

```javascript
type InputProps = {
  label: string,
  id: string,
  type: "text" | "number",
  disabled?: true | false,
};
export default function Input({ id, label, type, disabled }: InputProps) {
  return (
    <p>
      <label htmlFor={id}>{label}</label>
      <input id={id} type={type} disabled={disabled} />
    </p>
  );
}
```

# Using React ComponentProps

The issue with the simple approach described above is you have to predefine each of the attributes you think you need for the _HTML input element_. In some cases they are optional and you have to check for them (or undefined).

The `ComponentProps` type in React provides a way to define the props, in our case without the use of references (useRef). By specifying the `ComponentPropsWithoutRef<'input'>` literal you get all of the attributes for an **input** element.

Use of the JS spread operator **{...props}** facilitates their inclusion as attributes in the HTML input.

```javascript
import { type ComponentPropsWithoutRef } from "react";
type InputProps = {
  label: string,
  id: string,
} & ComponentPropsWithoutRef<"input">;

export default function Input({ id, label, ...props }: InputProps) {
  return (
    <p>
      <label htmlFor={id}>{label}</label>
      <input id={id} {...props} />
    </p>
  );
}
```

An unexpected side effect (a good one) of the use of the ComponentProps type was that the number input type HTML now behave like a number.

![Your Age Field is a Spinner](./screenshots/component-props-rendered-input.png)

# Wrapper Button Component

**Discriminated unions** and the **ComponentProp** type can be combined to provide a `Button component` that can display either a button or an archor HTML element.

```javascript
import { type ComponentPropsWithoutRef } from "react";
type ButtonProps = {
  el: "button",
} & ComponentPropsWithoutRef<"button">;
type AnchorProps = {
  el: "anchor",
} & ComponentPropsWithoutRef<"a">;

export default function Button(props: ButtonProps | AnchorProps) {
  const { el, ...otherProps } = props;
  if (props.el === "anchor") {
    return <a className="button" {...props} />;
  }

  return <button className="button" {...props} />;
}
```

# Custom Form Component

In addition to the usual props captured by the ComponentPropsWithoutRef type, we want to also capture the event handler associated with the form submission, we'll also include the a custom prop to it that provides it.

```javascript
import { type ComponentPropsWithoutRef, type FormEvent } from "react";
type FormProps = ComponentPropsWithoutRef<"form"> & {
  onSave: (value: unknown) => void,
};
```

**Note**: The `value` argument of `unknown` defines the incoming submit event handler as **unknown** because we don't know in advance what the data in the form will be.

```javascript
export default function Form(props: FormProps) {
  function submitHandler(event: FormEvent<HTMLFormElement>) {
    event.preventDefault();

    // FormData requires the "name" attribute.
    // The List component was updated to include it (from "id")
    const formData = new FormData(event.currentTarget);
    const data = Object.fromEntries(formData);
    props.onSave(data);
  }

  return (
    <form onSubmit={submitHandler} {...props}>
      {props.children}
    </form>
  );
}
```

The finished `Form` wrapper component is provided below.

```javascript
import { type FormEvent, type ComponentPropsWithoutRef } from "react";
type FormProps = ComponentPropsWithoutRef<"form"> & {
  onSave: (value: unknown) => void,
};

export default function Form({ onSave, children, ...otherProps }: FormProps) {
  function submitHandler(event: FormEvent<HTMLFormElement>) {
    event.preventDefault();

    // FormData requires the "name" attribute.
    // The List component was updated to include it (from "id")
    const formData = new FormData(event.currentTarget);
    const data = Object.fromEntries(formData); //converts the formatData to object
    onSave(data);
  }

  return (
    <form onSubmit={submitHandler} {...otherProps}>
      {children}
    </form>
  );
}
```

The App component shown below implements all of the wrappers.

Note in the `saveHandler event handler` below the type matches the **unknown** type as defined in the wrapper `Form component`. However, a conversion of _data_ using the **as keyword** redefines it using the form types defined here.

```javascript
import Input from './Input.tsx';
import Button from './Button.tsx';
import Form from './Form.tsx';

export default function App() {
  function saveHandler(data: unknown) {
    const extractedData = data as {name: string, age: string};
    console.log(extractedData);
  };

  return (
    <main>
      <Form onSave={saveHandler}>
        <Input type="text" label="Name" id="name" />
        <Input type="number" label="Age" id="age" />
        <p>
         <Button el="button">Save</Button>
        </p>
      </Form>
    </main>
  )
}
```
