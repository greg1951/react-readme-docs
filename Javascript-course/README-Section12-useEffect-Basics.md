# The useEffect Hook
React components should never interact with the outside world but should instead defer those interactions to events handlers and to the React ```useEffect``` hook.

1. [The useEffect Hook](#the-useeffect-hook)
2. [Simple useEffect Example](#simple-useeffect-example)
3. [Side Effect Example](#side-effect-example)
4. [Correct useEffect Example](#correct-useeffect-example)
5. [The useEffect Dependencies](#the-useeffect-dependencies)
6. [Mechanics of useEffect](#mechanics-of-useeffect)
7. [Timeline of Effects](#timeline-of-effects)
8. [Best useEffect Practices](#best-useeffect-practices)
9. [The useEffect Cleanup Function](#the-useeffect-cleanup-function)
10. [Cleaning Up Data Fetch Requests](#cleaning-up-data-fetch-requests)


# Simple useEffect Example
The example below is a crude example of how to make a fetch request inside a ```useEffect``` hook. 

```javascript
  useEffect(function () {

    fetch(`http://www.omdbapi.com/?apikey=${KEY}&s=${FAV_MOVIE}`)
      .then((res) => res.json())
      .then((data) => setMovies(data.Search));
    }, 
    []);
```

In the example above, the fetch method was implemented as an initial React render, as there are no dependencies (i.e. empty array) defined in the second argument of the ```useEffect``` hook. 

# Side Effect Example
This fetch operation should never be implemented inside a React component, as the state update done by the ```setMovies``` statement would cause a side effect, in this case, endless looping on the network by the fetch operation.

# Correct useEffect Example
The ```fetch``` example below is the correct **async implementation**.

```javascript
  useEffect(function () {
    async function fetchMovies()  {
      const res = await 
        fetch(`http://www.omdbapi.com/?apikey=${KEY}&s=${FAV_MOVIE}`);
      const data = await res.json();
      setMovies(data.Search);
      };
      fetchMovies();
    },[]);
```
# The useEffect Dependencies
In the above example, the effect's dependencies were null, in order for the hook to run at the initial rendering. However, in practice, the dependency array is used to define variables that when they change, they will cause the effect to re-execute.

# Mechanics of useEffect

The ```useEffect``` then is like an event listener, listening for changes to state or props that cause the effect to run again. **It is a  mechanism to keep the component synchronized with an external system.** When the dependencies (state/props) change, the effect reruns to the component renders its view again.

Example 1 of 3
```javascript
useEffect(fn, [x, y, z]);
```
In this case, the effect will run on the initial mount and when any of *x, y, or z* change.

Example 2 of 3
```javascript
useEffect(fn, [])
```
The effect synchronized with no state or props and only runs on initial render (mount)

Example 3 of 3
```javascript
useEffect(fn);
```
Without any dependecies it means the effect synchronizes with everything, it runs on ever render. **This is usually not good.**

# Timeline of Effects
Effects do not run at the time of component rendering. They actually occur in the Commit phase, when the DOM is updated and the Browser Paint phase and rendered the page. Remember, effects are batched and run asynchronously, as there may be longer running processes involved.

To demonstrate the timeline consider the following log messages in the App component.

```javascript
  ...
  useEffect(function () {
    console.log("A");
  }, []);

  useEffect(function() {
    console.log("B");
  });

  console.log("C");
  ...
```
When saved, the console will show **C, then A, then B**. Remember the effects run after the browser paint, so C precedes the others. 

Now if you enter some value into the search bar of the app where state is updated, then what you see is **C, then B, then C, then B**. Since we are past the initial render, there is no A. Instead The B effect will run after each render because it runs after everything.

# Best useEffect Practices
In the example below, the fetch request for movies was moved into into a new .js file (**useMovies.js**). The logic in this new .js file also contains robust error handling to deal with a number of scenarios that can occur when making asynchronous API calls (e.g. network loss, not found).

```javascript
export function useMovies(query) {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState("");

  useEffect(
    function () {
      // callback?.();

      const controller = new AbortController();

      async function fetchMovies() {
        try {
          setIsLoading(true);
          setError("");

          const res = await fetch(
            `http://www.omdbapi.com/?apikey=${KEY}&s=${query}`,
            { signal: controller.signal }
          );

          if (!res.ok)
            throw new Error("Something went wrong with fetching movies");

          const data = await res.json();
          if (data.Response === "False") throw new Error("Movie not found");

          setMovies(data.Search);
          setError("");
        } catch (err) {
          if (err.name !== "AbortError") {
            console.log(err.message);
            setError(err.message);
          }
        } finally {
          setIsLoading(false);
        }
      }

      if (query.length < 3) {
        setMovies([]);
        setError("");
        return;
      }

      fetchMovies();

      return function () {
        controller.abort();
      };
    },
    [query]
  );
```

In the App component then the reference to the query state now references the useMovies.js, looking for a state change to ```query```.

```javascript
import { useEffect, useRef, useState } from "react";
import { useMovies } from "./useMovies";
...
export default function App() {
  const [query, setQuery] = useState("");
  const { movies, isLoading, error } = useMovies(query);
  ...
```
# The useEffect Cleanup Function
The cleanup function is optional code that can run when a component is destroyed (unmounted). It is run on two occasions: 

1. Before the effect is executed  
2. After the component has unmounted

Some examples of effects you may want to cancel are listed below.

- HTTP request --> Cancel request
- API subscription --> Cancel subscription
- Start timer --> Stop timer
- Add event listed --> Remove listener

As a best practice, if you have multiple effects that need to be cleaned up, writer multiple cleanups, one for each. This makes the code more understandable (and maintainable).

The cleanup function is a function returned by the effect that contains the logic to do it. In the example below, the title in the browser will be reset to a default value. When the title prop is undefined because a movie was removed from a list, then the function inside the useEffect is run.

```javascript
  useEffect(
    function () {
      if (!title) return;
      document.title = `Movie | ${title}`;

      return function () {
        document.title = "usePopcorn";
        console.log(`Clean up effect for movie ${title}`);
      };
    },
    [title]
  );
```

What's interesting is that even though the component was unmounted, the console message will still display the value of the title prop. This is due to Javascript closure: the title variable existed at the time the returned cleanup function was created, and even though unmounted, the variable was still available.

# Cleaning Up Data Fetch Requests

Imagine that on the movie search, there are requests made on each keystroke to retrieve movies, based on that string input. There will be multiple network requests made for each, and each represents a race condition. What you would like to do is to cancel the request for the previous keystroke.

This is being done in the useMovies.js. To abort previous requests, a browser API object, AbortController will be used. The fetch request itself must be passed a 2nd parameter (**signal**) and it will be associated with the AbortController instance defined in the effect function. 

```javascript
  useEffect(
    function () {
      const controller = new AbortController();
      ...
          const res = await fetch(
            `http://www.omdbapi.com/?apikey=${KEY}&s=${query}`,
            { signal: controller.signal }
          );
      ...
      return function () {
        controller.abort();
      };

```


