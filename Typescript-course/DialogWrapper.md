1. [Summary](#summary)
2. [Create the VS Code Project](#create-the-vs-code-project)
3. [Stage 1: Basic Embedded Dialog](#stage-1-basic-embedded-dialog)
4. [Stage 2: Dialog Using Component Content](#stage-2-dialog-using-component-content)
5. [Stage 3: Creating Separate Dialog Component](#stage-3-creating-separate-dialog-component)
   1. [Create the Dialog Component](#create-the-dialog-component)
   2. [Refer to the Dialog Component in App](#refer-to-the-dialog-component-in-app)

# Summary
Creating a Typescript dialog component is the subject here. This example was derived from [Chris Pennington's YouTube video](https://www.youtube.com/watch?v=YwHJMlvZRCc). I'm basically just summarizing what Chris instructed in his 15 minute video by showing the React code in various stages of development.

# Create the VS Code Project
1. In the project directory run: `npm create vite@latest dialog-react`
2. Change into the `dialog-react` project directory
3. Verify the project builds: `npm run dev`

# Stage 1: Basic Embedded Dialog
The code for this section will be implemented in the `App.tsx` component.

1. Open the `src/App.tsx` file. 
2. Replace the code in the return with that shown below 

```javascript
import './App.css'
import { useRef } from 'react'
function App() {
    const dialogRef = useRef<HTMLDialogElement>(null);

    function toggleDialog() {
        if (!dialogRef.current) {
        return;
        }
        if (dialogRef.current.hasAttribute("open")) 
            dialogRef.current.close()
        else 
            dialogRef.current.showModal();
    }

  return (
    <>
      <p>Dialog Example</p>
      <div ref={dialogRef} style={{
        display: "flex",
        gap: "2rem",
      }}>
        <button onClick={toggleDialog}>Button 1</button>
        <button onClick={toggleDialog}>Button 2</button>
      </div>
      <dialog ref={dialogRef}>Dialog Content</dialog>
    </>
  );
}
export default App
```
**Notes:**
- Showing and closing the DOM `Dialog` element in the Javascript function `toggleDialog` requires the Dialog element have a reference the function can use. The React `useRef` is used for this purpose.
- Using Typescript requires the generic Type of `HTMLDialogElement` in the useRef hook usage.
- When run, the web page will show the two buttons' with static content. When a button is clicked, the button content is shown. The `ESC` key can be used to exit the button dialog (or refresh the page).

# Stage 2: Dialog Using Component Content
There are a number of features missing in the Dialog.
- Modal content is static: Instead, **reference different component content** in for each button.
- Only the `ESC` key can be used to close the dialog: provide a button in the dialog to do this. In addition, if the user clicks anywhere outside of the dialog, it should close it.

```javascript
import './App.css'
import { useRef, useState, type ReactNode } from 'react'
import ModalContent1 from './components/ModalContent1';
import ModalContent2 from './components/ModalContent2';

function App() {
  const [dialogContent, setDialogContent] = useState<ReactNode>(null);
  const dialogRef = useRef<HTMLDialogElement>(null);
  function toggleDialog() {
    if (!dialogRef.current) {
      return;
    }
    if (dialogRef.current.hasAttribute("open")) 
      dialogRef.current.close()
    else 
      dialogRef.current.showModal();
  }

  return (
    <>
      <p>Dialog Example</p>
      <div style={{
        display: "flex",
        gap: "2rem",
      }}>
        <button onClick={() => {
          setDialogContent(<ModalContent1/>);
          toggleDialog();
        }}>Button 1</button>
        <button onClick={() => {
          setDialogContent(<ModalContent2/>);
          toggleDialog();
        }}>Button 2</button>
      </div>
      <dialog ref={dialogRef} onClick={(e) => {
        if (e.currentTarget === e.target)
          toggleDialog();
      }}>
        <div>
          {dialogContent}
          <button onClick={toggleDialog}>Close</button>
        </div>
      </dialog>
    </>
  )
}

export default App
```
**Notes:**
- Two new components `ModalContent1/2` component were created in the src/components (new) directory, returning unique content for each, something like that shown below.

```javascript
export default function ModalContent1() {
    return <p>Modal Content 1</p>
}
```
- The `useState` hook is needed so that the custom dialog content returned by the `ModalContent1/2` components can be shown in the dialog.

- The button `onClick` events were changed to defer the execution to the inline code that sets the `dialogContent` state, before rendering the Dialog.

- **An `onClick` event was added to the Dialog element** to provide the ability to close an open dialog when the user clicks anywhere outside of it.

    ```javascript
          <dialog ref={dialogRef} onClick={(e) => {
          if (e.currentTarget === e.target) 
            toggleDialog();
      }}>
    ```

    **Note**: The `e.current.target` in the event is the dialog itself. The surrounding area immediately surrounding the current target is referenced by `e.target`. 

# Stage 3: Creating Separate Dialog Component
In this stage we'll componentize the Dialog element in the App component into a separate and reusable application component. 
- Most of the setup for that is standard React component `children` (ReactNode) and props (for the `toggleDialog` event). 

- However, the `useRef` hook using in the Dialog element requires special handling in the new Dialog component, as React **Reference Forwarding**. 

## Create the Dialog Component

1. Define a new file named `src/components/Dialog.tsx`. 
2. Import the forwardRef function. 
**Note:** A forwardRef lets your component expose a DOM node to a parent component using a ref.

```javascript
import { type ReactNode, forwardRef } from "react";
type DialogProps = {
    children: ReactNode;
    toggleDialog: () => void;
}

const Dialog = forwardRef<HTMLDialogElement, DialogProps> 
    (({children, toggleDialog}, dialogRef) => {
      return (
        <dialog ref={dialogRef} onClick={(e) => {
            if (e.currentTarget === e.target)
                toggleDialog();
            }}>
            <div>
                {children}
                <button onClick={toggleDialog}>Close</button>
            </div>
      </dialog>
    );
})
export default Dialog
```
**Notes:**
- What is shown in the return is essentially the content of the dialog element in the App component. It was cut from the App.tsx file and pasted altered slightly to use `children` and the `toggleDialog` event. 

- The React `forwardRef function signature` is shown below. It takes a Type and a second argument containing a Propeties object.
    ```javascript
    function forwardRef<T, P = {}
    ```
= The DialogProps type was defined to reference the children and toggleDialog parameters. 
- The **entire Dialog** must be enclosed in an extra set of parentheses.

## Refer to the Dialog Component in App 
The easy part of the change was to Add the new Dialog component where the Dialog element used to be in the component.

```javascript
import './App.css'
import { useRef, useState, type ReactNode } from 'react'
import ModalContent1 from './components/ModalContent1';
import ModalContent2 from './components/ModalContent2';
import Dialog from './components/Dialog';

function App() {
  const [dialogContent, setDialogContent] = useState<ReactNode>(null);
  const dialogRef = useRef<HTMLDialogElement>(null);

  function toggleDialog() {
    if (!dialogRef.current) {
      return;
    }
    if (dialogRef.current.hasAttribute("open")) 
      dialogRef.current.close()
    else 
      dialogRef.current.showModal();
  }

  return (
      <>
      <p>Dialog Example</p>
        <div style={{
            display: "flex",
            gap: "2rem",
        }}>
        <button onClick={() => {
            setDialogContent(<ModalContent1/>);
            toggleDialog();
        }}>Button 1</button>
        <button onClick={() => {
            setDialogContent(<ModalContent2/>);
            toggleDialog();
        }}>Button 2</button>
        </div>
        
      <Dialog ref={dialogRef} toggleDialog={toggleDialog}>
        {dialogContent}
      </Dialog>
      </>
  );
};
export default App
```



