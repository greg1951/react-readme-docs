# Using Zod Typescript Validation

The Zod library provides an easy way to define schema that Typescript can use to validate Typescript in the application.

# Setup

Simply run `npm install zod` in a project terminal.

# App Component

Here is the Zod implementation of the project. ([The main branch in GitHub](https://github.com/greg1951/react-typescript-data-fetching) contains the original, non-Zod project. The [feature/zod branch](https://github.com/greg1951/react-typescript-data-fetching/tree/feature/zod) has all of the Zod code.)

```javascript
import { type ReactNode, useEffect, useState } from "react";
import { BlogPost } from "./components/BlogPosts";
import BlogPosts from "./components/BlogPosts";
import { get } from "./components/util/http";
import fetchingImg from "./assets/data-fetching.png";
import { z } from "zod";

// Two new Zod schemas below
const rawDataBlogPostSchema = z.object({
  id: z.number(),
  userId: z.number(),
  title: z.string(),
  body: z.string(),
});
const expectedResponseDataSchema = z.array(rawDataBlogPostSchema);

function App() {
  const [fetchedPosts, setFetchedPosts] = useState<BlogPost[]>();
  const [isFetching, setIsFetching] = useState(false);

  useEffect(() => {
    setIsFetching(true);
    async function fetchPosts() {
      try {
        const data = await get("https://jsonplaceholder.typicode.com/posts");

        //Convert from API response to BlogPosts component format
        const parsedData = expectedResponseDataSchema.parse(data);
        const blogPosts: BlogPost[] = parsedData.map((rawPost) => {
          return {
            id: rawPost.id,
            title: rawPost.title,
            text: rawPost.body,
          };
        });
        setIsFetching(false);
        setFetchedPosts(blogPosts);
      } catch (error) {
        if (error instanceof Error) {
          console.error(error.message);
        }
      }
    }
    fetchPosts();
  }, []);

  let content: ReactNode;

  if (isFetching) {
    content = <p id="loading-fallback">Fetching posts...</p>;
  }

  if (fetchedPosts) {
    content = <BlogPosts posts={fetchedPosts} />;
  }

  return (
    <main>
      <img src={fetchingImg} alt="Posts Fetch Image" />
      {content}
    </main>
  );
}

export default App;
```

**Notes:**

- Two new Zod schemas were added, above the start of the App component. (That location ensures the schemas are only created once.)

- The `data` variable constaining the posts retrieved from the `get` utility function are simply converted, without the necessity for an alias.

- The `parse` method associated with the Zod `expectedResponseDataSchema` schema knows about the schema and allows a conversion by Zod. **Didn't have to write the conversion.**

- For comparison purposes, here is what was required **without Zod**.

```javascript
        async function fetchPosts() {
            const data = (await get(
            "https://jsonplaceholder.typicode.com/posts"
            )) as RawDataBlogPost[];

            const blogPosts: BlogPost[] = data.map((rawPost) => {
                return {
                id: rawPost.id,
                title: rawPost.title,
                text: rawPost.body,
                };
            });
```
