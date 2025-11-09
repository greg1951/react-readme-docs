1. [Overview](#overview)
2. [ReactRouter](#reactrouter)
3. [Project Structuring](#project-structuring)
4. [ReactRouter Navigation](#reactrouter-navigation)
   1. [The Link Component](#the-link-component)
   2. [The useHistory Hook](#the-usehistory-hook)
5. [The Layout Component](#the-layout-component)
6. [The Card Component](#the-card-component)
   1. [CSS Modules](#css-modules)
7. [Google Firebase](#google-firebase)
8. [React Context](#react-context)
   1. [Configuring the Context](#configuring-the-context)
   2. [Using the Context](#using-the-context)


# Overview

This project was built from Max's ReactJS refresher module, **sections 49-85**. The source code can be found in the [master branch of this Git repo](https://github.com/greg1951/max-reactjs-refresher-project/tree/master). More details follow the summary provided below. 

-  Routing to different pages using the `ReactRouter`
-  `Project structuring` provided reasonable separation of concerns
-  Other ReactRouter features like `Link` and hooks (e.g. `useHistory`)
-  `Layout` component for navigation header. Props children allow pages to be shown below the header.
- Wrapping of various components by the `Card` component which leverages React props children.  
- Form wrapped by Card component that utilizes various hooks: `useRef`, `useHistory`
- Use of CSS `modules` to provide components specific styling.
- Use of `Google Firebase` database to persist new meetups
- React `Context` library was used for state management of Favorites

# ReactRouter
The router will allow for page sepaation of components so that separate navigation to application pages would be provided. [The goal was to produce the following UI.](./docs/reactjs-refresher-output.png)

1. After installing the router (`npm install --save react-router-dom@5`), the first task was to implement the `BrowserRouter` component. That component uses regular URL paths. 

2. This was done in the `index.js`, which wrapped the App component initiated there. 

3. The `App` component was modified to implement `Switch` and `Route` route matchers, to direct to application pages. The Switch matcher will search through its children (Route) elements to find a route that matches the current URL.

4. A `Layout` component was implemented and included in App as a way to position a navigation bar at the top of each page.

# Project Structuring
The application components built for the project were placed into various subfolders inside the [src/components](./docs/reactjs-source-project-structure.png) directory. It seemed to be a relatively straight-forward organization of the project artifacts.

- The `Layout` folder contains the Layout.js and MainNavigation.js files, and their associated CSS module files.
- The `Meetups` folder contains the MeetupList.js, MeetupItem.js, NewMeetupForm.js, and their association CSS module files.
- The `ui` folder contains the Card.js which was used throughout the project.
- The application pages rendered by the Router were consolidated into the `pages` folder which contains AllMeetups.js, Favorites.js, NewMeetup.js and their associated CSS files.
- The React Context component was defined in the `store` folder.

# ReactRouter Navigation
The React Router includes various components and hooks to navigate the defined Route pages.

## The Link Component
This component provides a declarative way to navigate an application, taking the place of an anchor element. The `src/components/layout/MainNavigation.js` provides a good example of it, where the `to` attribute specifies the path.

```javascript
function MainNavigation() {
    return (
        <header className={styles.header}>
            <div className={styles.logo}>React Meetups</div>
            <nav>
                <ul>
                    <li><Link to='/'>Home</Link></li>
                    <li><Link to='/new-meetup'>New Meeting</Link></li>
                    <li><Link to='/favorites'>Favorites</Link></li>
                </ul>
            </nav>
        </header>
    )
}
```

## The useHistory Hook
The useHistory hook provides a way to navigate to a route (see the src/components/meetups/`NewMeetupForm.js`). You first retrieve the hook function and then run it (like inside an event handler when a form submission was run).

```javascript
    ...
    const history = useHistory();
    ...
    function addButtonHandler(event) {
        ...
            postMeetupDataHandler(meetupData);
            /* use history.replace rather than history.push so the back browser button can't be used */
            history.replace('/');
```

# The Layout Component
The src/components/layout/`Layout` component was implemented as a way to include a navigation bar at the top of each page. What is very clever about the component is that application pages are then rendered as `props children` of the Layout.

```javascript
function Layout({children}) {
    return (
        <div>
            <MainNavigation />
            <main className={styles.main}>
                {children}
            </main>
        </div>
    );
}
export default Layout;
```

# The Card Component
The entire src/components/ui/`Cards.js` file is shown below as it is incredibly short and sweet. It renders anything that Card wraps as a child. 

```javascript
import styles from './Card.module.css';

function Card({children}) {
    return (
        <div className={styles.card}>
            {children}
        </div>
    );
}
export default Card;
```
What the component basically provides is a CSS module to provide the card formatting.
```css
.card {
  padding-top: 1px;
  background-color: white;
  border-radius: 6px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
}
```
## CSS Modules
I go back and forth on whether to have bigger or smaller CSS files. At this point I'm convinced that CSS modules, associated with React components provide for more granular and specialized CSS files that are not going to collide with other CSS classes in the project. 

They are imported into the component and then referred to a objects as shown in the previous Card component section.

# Google Firebase

During the development lifecycle this Google framework can provide a realtime database that can be easily used to persist data. For testing in this project any meetup instances created in the application can easily persisted and retrieved using the Firebase APIs. 

Use your Google email to login to Google Firebase and create the project and the database. In the [Firebase console](./docs/reactjs-firebase-console.png) you will find the database URL.

To post data in the application, visit the src/components/pages/`NewMeetup.js` file. Shown below is the event handler to post a new meetup to the database.

```javascript
    ...
    async function postMeetupDataHandler(meetupData) {
        console.log(`posting title: ${ meetupData.title }`);
        const response =
            await fetch(
                'https://react-refresher-database-default-rtdb.firebaseio.com/meetups.json',
                {
                    method: 'POST',
                    body: JSON.stringify(meetupData),
                    headers: {
                        'Content-Type': 'application/json'
                    }
                }
            );
        console.log(response);
    }
    ...
```

**Notes**: 

- The `meetups.json` entry at the end of the URL represents the table that will be created in Firebase. The default method is GET, so here the method is defined as POST, to create a meetup.

- Firebase doesn't represent the table data as arrays but as an object containing 1:M meetup JSON objects. When fetching data then it needs to be restructured into an array, so it can be saved as state array in React. A sample is shown below.

    ```json
    {
        "meetups": {
            "-OdWPTz1KXubyEB_5HyP": {
            "address": "123 Main Street, Plano, Tx",
            "description": "A must attend meeting! ",
            "image": "https://upload.wikimedia.org/wikipedia/commons/thumb/d/d3/Stadtbild_M%C3%BCnchen.jpg/2560px-Stadtbild_M%C3%BCnchen.jpg",
            "title": "Meeting 1"
            },
            "-OdWPoGX6NPDfHFgZ9kN": {
            "address": "123 Main Street",
            "description": "Must attend!\n",
            "image": "https://upload.wikimedia.org/wikipedia/commons/thumb/d/d3/Stadtbild_M%C3%BCnchen.jpg/2560px-Stadtbild_M%C3%BCnchen.jpg",
            "title": "Meeting 1"
            }
        }
    }
    ```

The src/components/pages/`AllMeetups.js` file retrieves the Firebase meetup instances and converts the above "object array" into an array the application is expecting. (The fetch syntax is using the *promise* format rather than the preferred *await* format you saw in the previous POST fetch.)

```javascript
    useEffect(() => {
    fetch('https://react-refresher-database-default-rtdb.firebaseio.com/meetups.json')
    .then((response) => {
        return response.json();
    })
    .then((data) => {
        const entryArray = Object.entries(data);
        let returnArray = entryArray.map((entry, index) => {
        const mapData = {
            id: entry[0],
            title: entry[1].title,
            address: entry[1].address,
            image: entry[1].image,
            description: entry[1].description
        }
        return mapData;
        });
        setIsLoading(false);
        setMeetupData(returnArray);
    });
    }, []);
```

# React Context

The React Context component provides a way to pass data through the component tree without having to use extensive prop drilling. There are global values like user authentication, theme styling, or preferred language that must be accessible in the component tree without the use of prop drilling. 

## Configuring the Context

The global context provider often wraps the entire application, as it does in this case. See the `index.js` component below.

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import { FavoritesContextProvider } from './store/favorites-context';

import App from './App';

ReactDOM.render(
    <FavoritesContextProvider>
        <BrowserRouter>
            <App />
        </BrowserRouter>
    </FavoritesContextProvider>,
    document.getElementById('root')
);
```

In simpler projects (like this one) Context can be used to manage state. However, in larger applications, Redux is a better choice as state management is its purpose. In the project the Meetup favorites are to be kept in React state, using the React Context provider. 

Although state isn't the ideal choice for Context it does serve as an example. In the example below the Context store definition (see src/component/store/`favorites-context.js`) is provided in its entirety. There are notes interspersed in the code as well as after it.

In the `favorites-context.js` file there are two main artifacts that are used by any components that need to interact with it: `FavoritesContext` and `FavoritesContextProvider`, which are both **exported**.

```javascript
import { createContext, useState } from "react";

/* Defines initial state managed in this Context */
const FavoritesContext = createContext({
    favorites: [],
    totalFavorites: 0,
    /* lines below are not necessary; they are here only to assist with auto-completion later on */
    addFavorite: (favoriteMeetup) => {},
    removeFavorite: (meetupId) => {},
    itemIsFavorite: (meetupId) => {}
});

/* The exported provider will be used to wrap components needing access to this Context */
export function FavoritesContextProvider(props) {
    const [userFavorites, setUserFavorites] = useState([]);

    function addFavoriteHandler(favoriteMeetup) {
        /* Use callback function because state is updated async and this ensures previous state is used */
        // console.log(favoriteMeetup);

        setUserFavorites((previousUserFavorites) => {
            return previousUserFavorites.concat(favoriteMeetup);
        });
    }

    function removeFavoriteHandler(meetupId) {
        setUserFavorites(previousUserFavorites => {
            return previousUserFavorites.filter((meetup) => meetup.id !== meetupId);
        });
    }

    function itemIsFavoriteHandler(meetupId) {
        return userFavorites.some((meetup) => meetup.id === meetupId);
    }

    /* The context object below is the object value contained in the provider used by the app */
    const context = {
        favorites: userFavorites,
        totalFavorites: userFavorites.length,
        addFavorite: addFavoriteHandler,
        removeFavorite: removeFavoriteHandler,
        itemIsFavorite: itemIsFavoriteHandler
    };

    /* The props.children wrap the portion of the component tree that need access to it */
    return (
        <FavoritesContext.Provider value={context}>
            {props.children}
        </FavoritesContext.Provider>
    )
}

/* Export the Context object for used by components needing access to it */
export default FavoritesContext;
```

Notes:

- The `FavoritesContext` is defined first as it contains the interface elements (not the actual implementation) exposed by the Context. There are methods exposed as well so state can be added or removed. They are defined as function prototypes.

- The actual implementation of the Context is located in the `FavoritesContextProvider` function. 

- Notice that in the FavoritesContextProvider, the `Context` object defines the mapping between the `FavoritesContext` and the implementation. 

    ```javascript
        const context = {
        favorites: userFavorites,
        totalFavorites: userFavorites.length,
        addFavorite: addFavoriteHandler,
        removeFavorite: removeFavoriteHandler,
        itemIsFavorite: itemIsFavoriteHandler
    };
    ```

## Using the Context

The following snippet was taken from the src/components/meetups/`MeetupItem.js` file and shows how the Context is used by the component.

```javascript
import FavoritesContext from '../../store/favorites-context';
import Card from '../ui/Card';
import styles from './MeetupItem.module.css';
import { useContext } from 'react';

function MeetupItem(props) {
    const favoritesCtx = useContext(FavoritesContext);
    const itemIsFavorite = favoritesCtx.itemIsFavorite(props.id);
    
    function toggleFavoriteStatusHandler() {
        if (itemIsFavorite) {
            favoritesCtx.removeFavorite(props.id);
        }
        else {
            favoritesCtx.addFavorite({
                id: props.id,
                title: props.title,
                description: props.description,
                image: props.image,
                address: props.address,
            });
        }
    }
    ... In the JSX:
                    <div className={ styles.actions }>
                        <button onClick={toggleFavoriteStatusHandler}>
                            {itemIsFavorite? 'Remove Favorite' : 'Add Favorite'}
                        </button>
                    </div>
    ...
```

