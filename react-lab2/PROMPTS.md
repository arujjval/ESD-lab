**Prompt log — React Lab 2**  
- **Name and roll number:** Arujjwal Negi — MT2026014 
- **AI tool and plan:** ChatGPT  
  
---  
**Prompt 1 — generate**  
**Sent:**  
```  
CONTRACT.md prompt 1, unchanged  
   
```  
**What came back:**  
The initial implementation was organized into the four required files under `src/courses/`:  
- `useCourses.js` — a custom hook using `useState` and `useEffect` to load courses through `apiFetch`, maintain loading/error state, and add a course through the POST endpoint.  
- `CoursesView.jsx` — a presentational component that renders loading, error, empty, and populated states.  
- `AddCourseForm.jsx` — a controlled form for the six course fields, with submission state and API validation feedback.  
- `CoursesPage.jsx` — connects the hook, list view, and add form.  
The implementation used the React hooks and API helper specified by the contract. It did not use a separate data-fetching library or direct `fetch` calls.  
**Did it run?**  
The conversation did not establish a complete `npm test` result or verify every browser behaviour from the contract. I therefore have not marked the full feature as passing. The later stretch test output is recorded in the debugging prompt below.  
**What I did with it:**  
I used the generated structure as the starting point and continued with the search pre-lab tasks. I treated the contract as the source of truth and checked the individual components against its requirements.  
  
---  
**Prompt 2 — implement the course search UI**  
**Why I sent it:**  
The pre-lab section 06 skeleton contained a placeholder. I needed the component to own the search text, call the search hook, and pass its result to the presentational component.  
**Sent:**  
```  
Implement the pre-lab section 06 CourseSearch component using the supplied skeleton and requirements.  
   
 The component should:  
 - Have a heading: "Search the catalogue".  
 - Render a label "Search courses" and a controlled input of type="search".  
 - Store the typed query in component state.  
 - Call useCourseSearch(query).  
 - Pass the returned search data to SearchResults.  
 - Keep the component responsible only for the query state and composing the search UI.  
   
 Use the existing imports and named export from the skeleton.  
   
```  
**What came back:**  
A `CourseSearch` component using `useState` for the query, a controlled search input, `useCourseSearch(query)`, and `SearchResults` for rendering the outcome. The input was connected to its label using `htmlFor` and `id`.  
**What I kept, and what I rejected:**  
I kept the controlled-input approach and the separation between the search UI, search hook, and results view. No explicit rejected implementation was recorded.  
  
---  
**Prompt 3 — implement SearchResults**  
**Why I sent it:**  
The supplied `SearchResults.jsx` skeleton specified the exact UI for each search status. I needed a component that only renders from props and does not fetch data or own state.  
**Sent:**  
```  
Implement SearchResults({ results, status }) from the supplied skeleton.  
   
 Follow these rendering rules exactly:  
 - status "idle" -> return null  
 - status "loading" -> render <p>Searching…</p>  
 - status "error" -> render <p>Search failed.</p>  
 - status "ready" with an empty results array -> render <p>No courses match.</p>  
 - status "ready" with results -> render a <ul> with one <li> per course.  
 - Each list item should display "course.code · course.title".  
 - Use course.id as the React key.  
   
 This component must only render from props. Do not add hooks, state, or API calls.  
   
```  
**What came back:**  
A presentational component with conditional rendering for the four statuses and a mapped list for successful results. Each course row used `course.id` as its key and displayed its code and title.  
**What I kept, and what I rejected:**  
I kept the status-based rendering and the component's separation from data fetching. No explicit rejected implementation was recorded.  
  
---  
**Prompt 4 — implement useCourseSearch**  
**Why I sent it:**  
The search hook needed to handle blank input, URL encoding, loading and error states, changes to the query, and stale responses when a newer search replaces an older one.  
**Sent:**  
```  
Implement useCourseSearch(query) using the supplied skeleton and requirements.  
   
 Requirements:  
 - Trim the query.  
 - If the trimmed query is blank, send no request and return { results: [], status: "idle" }.  
 - Otherwise make a GET request to /api/courses?q=<query> using apiFetch from ../api/client.js.  
 - Build the query string with URLSearchParams so special characters such as "R&D" are encoded safely.  
 - Return status "loading" while the request is pending, "ready" when courses are returned, and "error" if the request fails.  
 - Search again only when the trimmed query changes.  
 - Use an ignore flag in the effect cleanup so a response from an older query cannot update the screen after a newer query has started.  
 - Use only the hooks allowed by the lab instructions.  
   
```  
**What came back:**  
A hook using `useEffect` and `useState`, `URLSearchParams` to encode the query, and an `ignore` flag in the cleanup function to prevent stale requests from updating state. Its effect depended on the trimmed query.  
**What I kept, and what I rejected:**  
I kept the URL encoding, trimmed-query dependency, and cleanup-based stale-response protection. I also noted that returning idle state for a blank query should be immediate, rather than relying only on state updates scheduled inside the effect.  
  
---  
**Prompt 5 — implement useDebounced**  
**Why I sent it:**  
The stretch task required the search hook to wait until typing had stopped for 250 milliseconds. Without debouncing, each keystroke could trigger a separate API request.  
**Sent:**  
```  
Implement useDebounced(value, ms) from the supplied skeleton.  
   
 It should:  
 - Return the initial value immediately.  
 - Keep the previous settled value while the new value is changing.  
 - Use useState for the settled value.  
 - Use useEffect to start a timeout whenever value or ms changes.  
 - Update the settled value after ms milliseconds.  
 - Clear the previous timeout in the effect cleanup with clearTimeout.  
 - Return the settled value.  
   
 Then use it in CourseSearch as:  
 useCourseSearch(useDebounced(query, 250))  
   
```  
**What came back:**  
A custom hook that initialized the settled value from `value`, scheduled an update with `setTimeout`, and cancelled the previous timer with `clearTimeout` in the cleanup function. The three hook-specific tests later passed.  
**What I kept, and what I rejected:**  
I kept the timer-and-cleanup implementation. The first version of the hook itself passed its three dedicated tests.  
  
---  
**Prompt 6 — debug the debounced CourseSearch integration**  
**Why I sent it:**  
The stretch test showed that the hook's individual tests passed, but typing a word still produced one request per character. I needed to identify whether the issue was in the debounce hook or in how `CourseSearch` used it.  
**Sent:**  
```  
The test output shows:  
 - The three useDebounced hook tests pass.  
 - The CourseSearch debounced test fails.  
 - Expected request: GET /api/courses?q=learn  
 - Actual requests: GET /api/courses?q=l, le, lea, lear, and learn.  
   
 Diagnose the integration problem and show the corrected CourseSearch.jsx.  
 The search hook must receive the debounced query, not the raw input query.  
   
```  
**What came back:**  
The issue was identified in the integration: `CourseSearch` was still passing the original `query` to `useCourseSearch`. The correction was to call `useDebounced(query, 250)` and pass that returned value to `useCourseSearch`.  
**What I kept, and what I rejected:**  
I kept the corrected data flow:  
```  
const debouncedQuery = useDebounced(query, 250);  
 const { results, status } = useCourseSearch(debouncedQuery);  
   
```  
The test output provided in the conversation showed 3 of 4 stretch tests passing before this integration correction. A rerun after the correction was not recorded, so I have not claimed that all four tests passed.  
  
---  
**Afterwards**  
- **How many prompts did the whole feature take?** The log above records six implementation and debugging prompts for the work documented here, in addition to the original contract prompt.  
- **Which of your prompts would you write differently now, and how?** I would make the integration requirement explicit earlier: `CourseSearch` must pass the debounced query to `useCourseSearch`, not the raw input value. I would also ask for the hook's return shape and each component's props to be stated explicitly.  
- **One thing the rules in prompt 1 prevented (or failed to prevent):** The contract encouraged separation of concerns: API calls stayed in hooks, presentational components rendered from props, and the form owned its own input state. However, the initial integration did not automatically ensure that the search hook received the debounced value. The stretch test exposed that wiring mistake.  
