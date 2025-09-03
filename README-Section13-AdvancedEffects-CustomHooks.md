1. [About React Hooks](#about-react-hooks)
2. [Hook Rules](#hook-rules)
3. [More About useState Hook](#more-about-usestate-hook)
4. [The useRef Hook](#the-useref-hook)
5. [useState vs. useRef: When to use](#usestate-vs-useref-when-to-use)

# About React Hooks
There are over 20 React hooks, some well used like useState, useEffect, useReducer, but there are some less used, which is the reason for this section in the course.

# Hook Rules
These rules will always be caught by the VS Code linter, unless you disable the linter with the ```/* es-lint-disabled */``` comment (which you shouldn't do).

1. Hooks can only be called at the top of the app and not within conditional statements, nested functions, or an early return.
    
2. The order of the hooks is critical, as React creates a linked list of the referenced hooks at runtime, as a means to establish the order of the hooks. This is why rule #1 above is established.

3. Hooks can only be called from React functions.

# More About useState Hook

# The useRef Hook

# useState vs. useRef: When to use

 