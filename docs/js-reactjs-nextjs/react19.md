---
sidebar_position: 2
---

# WHAT'S NEW IN REACT 19

### A. New Hooks
1. **useOptimistic**
- **Purpose**: Provides instant feedback to users by updating the UI optimistically before an async operation completes. If the operation fails, the UI reverts to its original state.
- **Use case**: Submitting forms or performing CRUD operations where users benefit from immediate feedback.
- Example:

```js
import { useOptimistic } from "react";

const OptimisticUpdate = () => {
  const [optimisticState, setOptimisticState] = useOptimistic("Loading...");
  const handleAction = async () => {
    setOptimisticState("Saving...");
    try {
      await saveToDatabase();
      setOptimisticState("Saved!");
    } catch {
      setOptimisticState("Failed!");
    }
  };

  return <button onClick={handleAction}>{optimisticState}</button>;
};
```

2. **useFormState**
- **Purpose**: Centralizes the management of form input states, validations, and submission results.
- **Use Case**: Perfect for managing dynamic forms and reducing boilerplate code.
- Example:

```js
import { useFormState } from "react";

const MyForm = () => {
  const [formData, formAction] = useFormState((prevState, formValues) => {
    return formValues.get("name") === "React"
      ? { success: true, message: "Welcome!" }
      : { error: "Invalid name" };
  });

  return (
    <form action={formAction}>
      <input name="name" />
      <button>Submit</button>
      {formData.message && <p>{formData.message}</p>}
    </form>
  );
};
```

3. **useFormStatus**
- **Purpose**: Provides contextual information about the status of a form, such as whether it’s currently being submitted.
- **Use Case**: Ideal for disabling buttons or showing spinners during submission.
- Example:

```js
import { useFormStatus } from "react";

const SubmitButton = () => {
  const { pending } = useFormStatus();
  return <button type="submit" disabled={pending}>Submit</button>;
};
```

4. **use**
- **Purpose**: Allows reading the value of a Promise or Context directly in the render phase. Unlike other hooks, use can be used inside loops and conditions.
- **Use Case**: Useful for rendering async data directly without additional state management.
- Example:

```js
import { use } from "react";

const DataComponent = () => {
  const data = use(fetchData());
  return <div>{data.message}</div>;
};
```

5. **useActionState**
- **Purpose**: Handles state changes resulting from user actions, particularly useful in forms or workflows.
- **Use Case**: Simplifies tracking of both previous and updated states after an action is performed.
- Example:

```js
import { useActionState } from "react";

const MyForm = () => {
  const handleSubmit = async (prevState, formData) => {
    const newData = Object.fromEntries(formData.entries());
    return { ...prevState, ...newData };
  };

  const [state, formAction] = useActionState(handleSubmit, {});

  return (
    <form action={formAction}>
      <input name="username" />
      <button>Submit</button>
      <pre>{JSON.stringify(state)}</pre>
    </form>
  );
};
```

### B. Enhanced Server-Side Features
1. **Server Components**
- Enabling pre-rendering of components on the server helping to reduce client-side JavaScript bundle sizes.
2. **Server Actions**
- Allow client components to execute server-side async functions via the `use server` directive.
3. **New Static APIs**
- `prerender` and `prerenderToNodeStream`: designed for SSG; ensuring all data is preloaded before producing static HTML streams, enhancing SSR performance.

### C. Other key features
1. **Improve ref handling**
- Function components can now accept `ref` as a direct prop, eliminating the need for `forwardRef`.

2. **Hydration error diffs**
- React 19 introduces better diagnostics for SSR hydration mismatches, improving debugging during server-client transitions.

### Why React 19 Stands Out
- React 19 makes handling complex forms, async updates, and server-client integration more seamless than ever.
- With new hooks reduce boilerplate and enhance developer productivity.
- Features like Server Components and Server Actions also position React 19 as a leader in server-side rendering and hybrid frameworks.
