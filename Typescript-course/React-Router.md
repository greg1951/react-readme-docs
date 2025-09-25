# Summary
The final exercise of the Typescript React course was to build a ReactMentoring application that consisted of three main pages that were bundled into a React Router implementation. This topic will document the following areas of that exercise.

- To this point in my React learning I was more comfortable with SPAs and the [React Router](https://reactrouter.com/home) was new. 

- A separate Modal dialog component was used as well which forced me to learn how to implement various React hooks and Forward Referencing.

- The source code for this application can be found in the develop branch of [this Git repo](https://github.com/greg1951/typescript-zod-redux-book-session-exercise/tree/develop).

# The React Router

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




