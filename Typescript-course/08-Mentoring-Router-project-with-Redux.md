1. [Book Mentoring Sessions Exercise](#book-mentoring-sessions-exercise)
2. [GitHub Initialization Commands](#github-initialization-commands)
3. [Exercise Summary](#exercise-summary)
4. [Component Hierarchy](#component-hierarchy)
5. [The React Router](#the-react-router)
   1. [Project Setup](#project-setup)
   2. [The App Component](#the-app-component)
   3. [Root Page](#root-page)
   4. [MainHeader Page Navigation](#mainheader-page-navigation)
6. [Redux Implmentation](#redux-implmentation)
   1. [Redux TS Files](#redux-ts-files)
   2. [Updating State](#updating-state)

# Book Mentoring Sessions Exercise

This is the final exercise in the Max's Typescript React. I also incorporated Redux for state management. 

- The source code for this application can be found in the **develop** branch of [this Git repo](https://github.com/greg1951/typescript-zod-redux-book-session-exercise/tree/develop). This branch uses the React Context API. 

- To see the Redux implementation, consult the [feature/redux](https://github.com/greg1951/typescript-zod-redux-book-session-exercise/tree/feature/redux) branch on GitHub.

# GitHub Initialization Commands

Before running the commands, login to GitHub and create a private repo on GitHub. For the repo specify a `.gitignore` file using Node template. Create a README markdown file as well.

Copy the repo URL into the 2nd command below. Then run them in a terminal window in the new project.

```sh
git init
git remote add origin https://github.com/greg1951/typescript-zod-redux-book-session-exercise.git
git pull origin master
git add .
git commit -m "first commit"
git push -u origin master --force
git branch develop
git checkout develop
```

# Exercise Summary

The final exercise of the Typescript React course was to build a ReactMentoring application that consisted of three main pages that were bundled into a React Router implementation. This topic will document the following areas of that exercise.

- Install Redux library (did not implement Zod)
- Reusable Button component that renders a button or a link. Both _button_ and _Link_ should receive a "button" CSS class
- Reusable Input wrapper component with _label_ and _input_ in a _div_, using a spread operator to accept attribute props
- custom modal component built on _dialog_ and used for the **Book a Session** and **Upcoming Sessions**
- Book sessions managed centrally via Redux (not Context API as exercise prescribes)
- Header component will have buttons to drive session functions
  ![](./screenshots/book-session/book-session-sample-home-page.png)

- Browse available sessions page
  ![](./screenshots/book-session/browse-sessions.png)

- Sign up for a session page
  ![](./screenshots/book-session/Learn-More-Details-page.png)

- Book session dialog
  ![](./screenshots/book-session/book-session-dialog.png)
  
- Upcoming sessions, With Cancelation dialog
  ![](./screenshots/book-session/upcoming-sessions-with-cancelation-dialog.png)


# Component Hierarchy

The components are of various types located in the **src/components** directory. Some components are pages to be rendered, located in the **pages** subdirectory. All navigation occurs from the **navigation/Header** component and UI components are found in the **ui** subdirectory.

![](./doc/components.svg)

# The React Router
To this point in my React learning I was more comfortable with SPAs and the [React Router](https://reactrouter.com/home) was new. 

A separate Modal dialog component was used as well which forced me to learn how to implement various React hooks and Forward Referencing.

There are three modes to using the Router: `Declarative`, `Data`, and `Framework`. The Router features grow with each mode, i.e. Declarative provides a minimal feature set, Data provides more and Framework offers the most features. The Data mode was utilized in the exercise. 

## Project Setup
Overall I thought the recommended structuring made sense. The directory names should have all been lower-case, rather than mixed-case.

- As there are multiple pages involved in the application, it made sense to create a `src/Pages` directory. 
- The `pages/Home.tsx` is the landing page as you might guess. The App component is actually the root page in React so the Router configuration was done there. 
- The `src/components/Sessions` directory contains the components used by the pages.
- Generic UI components (e.g. `Button`, `Input`, and `Modal`) are located in the `src/components/UI` directory.
- React's Context API was used for state management. 

(I started building it in Redux but stopped that implementation to get it going in the exercise solution. **Note**: I'll create a feature branch on this project to do the Redux implementation.)

## The App Component

The `createBrowserRouter` function takes an array of RouteObject, each of which will establish a hierachy of webpages, via a `path` attribute. (**Signature**: `createBrowserRouter(routes: RouteObject[], opts?: DOMRouterOpts): Router`)  


```javascript
import { RouterProvider, createBrowserRouter } from 'react-router-dom';

import HomePage from './pages/Home.tsx';
import SessionsPage from './pages/Sessions.tsx';
import SessionPage from './pages/Session.tsx';
import Root from './pages/Root.tsx';

const Router = createBrowserRouter([
  {
    path: '/',
    element: <Root />,
    children: [
      {
        index: true,
        element: <HomePage />,
      },
      { path: 'sessions', element: <SessionsPage /> },
      { path: 'sessions/:id', element: <SessionPage /> },
    ],
  },
]);

function App() {
  return <RouterProvider router={Router} />;
}

export default App;
```
**Notes:**
- The `Root` page defines the parent page in the application will ensure that all pages in the app have the same look and feel, via the use of the `Outlet` React Router component (See next section for more on this). 

- The `HomePage` is established as the page that the child pages will be pinned to, via the `index: true` attribute. If a page is not matched in one of the path objects, by default it will be pinned to the HomePage.

- Each of the `element` attributes below will create a hierarchy of pages, beginning with the `Root` page.

## Root Page
Despite there being so few lines, there is a lot going in the Root page. 

```javascript
import SessionsContextProvider from '../store/sessions-context.tsx';
import { ErrorBoundary } from 'react-error-boundary';
import MainHeader from '../components/navigation/MainHeader.tsx';
import { Outlet } from 'react-router-dom';

export default function Root() {
  return (
    <SessionsContextProvider>
      <ErrorBoundary fallback={<p>An error occurred in the application.</p>}> 
        <MainHeader />
        <Outlet />
      </ErrorBoundary> 
    </SessionsContextProvider>
  );
}
```
**Notes:**

- State management is implemented in the app via `SessionContextProvider`.

- The `ErrorBoundary` is a friendlier way to mask application errors in the UI when they occur. They can also be used for logging of error information (as side effect).

- At the top of all of the app pages is a `MainHeader` component having links to navigate the app. 

Below the navigation header are the pages configured by the Router, using the `Outlet` React Router component. The main points of interest in using the React Outlet component are listed below.

- **Nested Routes**: When you define nested routes within your React Router configuration, the `Outlet` component in the parent route's element determines where the content of the matched child route will be rendered.

- **Layout Components**: Commonly used in layout components (e.g., a component containing a header, sidebar, and footer) to define the area where the specific page content corresponding to the current route should be displayed. This allows for consistent UI elements across different pages while dynamically rendering the main content.

- **Sharing Context**: Parent routes can pass context to their child routes through the <Outlet /> component using the context prop. Child routes can then access this context using the useOutletContext hook, facilitating data sharing between parent and child components in a nested routing structure.

## MainHeader Page Navigation
Most of the component is not shown here, only the relevant lines.

```javascript
import { NavLink } from 'react-router-dom';
...
      <header id="main-header">
        <h1>ReactMentoring</h1>
        <nav>
          <ul>
            <li>
              <NavLink to="/" className={({isActive}) => isActive ? 'active' : ''} end>Our Mission</NavLink>
            </li>
            <li>
              <NavLink to="/sessions" className={({isActive}) => isActive ? 'active' : ''}>Browse Sessions</NavLink>
            </li>
            <li>
              <Button onClick={showUpcomingSessions}>Upcoming Sessions</Button>
            </li>
          </ul>
        </nav>
      </header>
```
# Redux Implmentation

As I have already written a markdown file for a Redux implementation (see `Using-Redux-Cart-App`), I will only be showing main points of interest for the implementation in this project. (The source code for it can be found in the [feature/redux](https://github.com/greg1951/typescript-zod-redux-book-session-exercise/tree/feature/redux) branch on GitHub.) 

## Redux TS Files
The `src/store` directory contains three files (following recommendations).
- **Booking-slice.ts**: Definitions of `BookingItem` object and `BookingState` (array of BookingItem's). The `bookingSlice` defines reducers (`addBookingItem`, `removeBookingItem`)

- **store.ts**: The Redux Toolkit `configureStore` function is used to create a `store` object containing the reducers defined in the slice file above. In addition, the `BookingState` *type* is created here and used in various components. 
  **Note**: There is a BookingState type defined in the slice TS file but that is internal to that file only.

- **hook.ts**: Defines two custom hooks. The `useBookingSelector` hook will allow the state to be examined. The `useBookingDispatch` hook will allow the actions defined in the slice TS file to be invoked to update state. 

## Updating State
The state to be updated in the case here is to add state. The src/components/sessions/`BookingSession.tsx` file uses the `useBookingDispatch` hook to reference the `addBookingItem` action in the `handleSubmit`  JS event handler.

(Only pieces and parts of the component are shown below.)

```javascript
import { FormEvent, useEffect, useRef } from 'react';

import Modal, { ModalHandle } from '../ui/Modal.tsx';
import Input from '../ui/Input.tsx';
import Button from '../ui/Button.tsx';
import { addBookingItem, BookingItem } from '../../store/booking-slice.ts';
import { useBookingDispatch } from '../../store/hook.ts';

type BookSessionProps = {
  session: BookingItem;
  onDone: () => void; // remove BookSession component from the DOM
};

export default function BookSession({ session, onDone }: BookSessionProps) {
  const modal = useRef<ModalHandle>(null);
  const sessionDispatch = useBookingDispatch();

  // useEffect needed due to `open` method in dialog component
  useEffect(() => {
    if (modal.current) {
      modal.current.open();
    }
  }, []);

  function handleSubmit(event: FormEvent<HTMLFormElement>) {
    event.preventDefault();

    const formData = new FormData(event.currentTarget);
    const data = Object.fromEntries(formData);
    let newSession: NewSession = {...session, name: formName, email: formEmail};
    sessionDispatch(addBookingItem(newSession));
    onDone(); // will remove this component from DOM
  }

  return (   
    <Modal ref={modal} onClose={onDone}>
      <h2>Book Session</h2>
      <form onSubmit={handleSubmit}>
        
        <Input label="Your name" id="name" name="name" type="text" placeholder="John Smith" />
        <Input label="Your email" id="email" name="email" type="email" placeholder="js@whocares.com" />
        <p className="actions">
          <Button type="button" textOnly onClick={onDone}>
            Cancel
          </Button>
          <Button>Book Session</Button>
        </p>
      </form>
    </Modal>
  );
}
```
**Notes:**
- The Modal dialog component contains a form that references the `handleSubmit` Javascript function to create state for a booking item.

- In that function, a spread operator is used to include the name and email in state when assigning the newSession variable to the `addBookingItem` dispatch method.

- An `onDone` function call on the dialog, when cancelled or submitted will remove the dialog event listener from the DOM.

