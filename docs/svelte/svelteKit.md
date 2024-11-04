---
sidebar_position: 2
---

## PART 1: BASIC SVELTEKIT

1. **Introduction**
- Whereas `Svelte` is a component framework, `SvelteKit` is an app framework (or metaframework)
- `SvelteKit` apps are server-rendered by default for excellent first load performance and SEO.
-  Can transition to client-side navigation.

2. **Routing**

**Pages:**
- Use filesystem-based routing (are defined by the directories in your codebase).

**Layouts:**
- To share common UI.
```js
src/routes/
├ about/
│ └ +page.svelte
├ +layout.svelte
└ +page.svelte
```

→ `/about/+page.svelte` and `+page.svelte`  contain the same navigation UI.


A `+layout.svelte` file applies to every child route, including the sibling `+page.svelte` (if it exists). Can nest layouts to arbitrary depth.

**Route parameters:**
- To create routes with dynamic parameters, use square brackets around a valid variable name.
→ `src/routes/blog/[slug]/+page.svelte` will match `/blog/one`, `/blog/two`

3. **Loading data**
**Page data:**

SvelteKit's job boils down to 3 things:
- Routing — figure out which route matches an incoming request.
- Loading — get the data needed by the route.
- Rendering — generate some HTML (on the server) or update the DOM (in the browser).

→ Every page of your app can declare a `load` function in a `+page.server.js` file alongside the `+page.svelte` file.


```js
<!-- src/routes/blog/+page.server.js -->
export function load() {
	return   // call API and return data
}
```

We can access this data in `src/routes/blog/+page.svelte` via the `data` prop:
```js
<script>
	export let data;
</script>

<h1>blog</h1>
```

In case we'd like to respond with a 404 page when no post exists:
```js
import { error } from '@sveltejs/kit';
import { posts } from '../data.js';

export function load({ params }) {
	const post = posts.find((post) => post.slug === params.slug);

	if (!post) throw error(404);

	return {
		post
	};
}
```

**Layout data:**

Just as `+layout.svelte` files create UI for every child route, `+layout.server.js` files load data for every child route.
→ to avoid repeat data among pages.
https://learn.svelte.dev/tutorial/layout-data


4. **Headers and cookies**

_Setting headers_
- Inside a `load` function (as well as in form actions, hooks and API routes), able to access to a `setHeaders` function.
- Customise caching behaviour with the Cache-Control response header.
```js
export function load({ setHeaders }) {
	setHeaders({
		'Content-Type': 'text/plain'
	});
}
```

_Reading and writing cookies:_
- The `setHeaders` function can't be used with the `Set-Cookie` header. Instead, you should use the `cookies API`.
```js
export function load({ cookies }) {
	const visited = cookies.get('visited');

	return {
		visited: visited === 'true'
	};
}
```

To set a cookie, use `cookies.set(name, value, options)`
→ recommended that configure the `path` when setting a cookie. (`cookies.set('visited', 'true', { path: '/' });`).

**default:**
```json
{
	httpOnly: true,
	secure: true,
	sameSite: 'lax'
}
```


5. **Shared modules / The $lib alias**

Anything inside `lib` directory can be accessed by any module in src via the `$lib` alias.

6. **Forms**
**The `<form>` element:**
- Add `load` function in `src/routes/+page.server.js`.
-  Create a server-side action to handle the POST request in `src/routes/+page.server.js`
- The request is a standard `Request` object; `await request.formData()` returns a `FormData` instance.
```html
<form method="POST">
	<label>
		add a todo:
		<input
			name="description"
			autocomplete="off"
		/>
	</label>
</form>
```

**Named form actions:**
- We need to have multiple actions on a page, to do that, use named actions instead of `default` action.
```js
export const actions = {
	create: async ({ cookies, request }) => {
		const data = await request.formData();
		db.createTodo(cookies.get('userid'), data.get('description'));
	},

	delete: async ({ cookies, request }) => {
		const data = await request.formData();
		db.deleteTodo(cookies.get('userid'), data.get('id'));
	}
};
```
**Notes:** Default actions cannot coexist with named actions.;

- Specify action in form:
```html
<form method="POST" action="?/create">
```

**Notes:**
- The action attribute can be any URL
- You might have something like `/todos?/create`

**Validation:**
- Use `build-in` validation 
```js
<input
	name="description"
	autocomplete="off"
	required
/>
```

Handle error from action:
```js
export const actions = {
	create: async ({ cookies, request }) => {
		const data = await request.formData();

		try {
			db.createTodo(cookies.get('userid'), data.get('description'));
		} catch (error) {
			return fail(422, {
				description: data.get('description'),
				error: error.message
			});
		}
	}
```

**Notes:**
- Can return data from an `action` without wrapping it in `fail` — for example to show a success! message when data was saved — and it will be available via the `form` prop.

**Progresssive enhancement:**
-  Users do have JavaScript, so,  we can progressively enhance the experience.
-  Import the enhance function from `$app/forms`:
```js
<form method="POST" action="?/create" use:enhance>
```
It will:
- update the `form` prop
- invalidate all data on a successful response, causing load functions to re-run
- navigate to the new page on a redirect response
- render the nearest error page if an error occurs

**Customizing use:enhance:**
- `use:enhance` is very customizable — you can `cancel()` submissions, handle redirects, control whether the form is reset, and so on. See the [docs](https://kit.svelte.dev/docs/modules#$app-forms-enhance) for full details.



7. **API Routes:**

Fetch data from a `/roll` API route
**GET:**
```js
<!-- src/routes/roll/+server.js -->
import { json } from '@sveltejs/kit';

export function GET() {
	const number = Math.floor(Math.random() * 6) + 1;

	return new Response(number, {
		headers: {
			'Content-Type': 'application/json'
		}
	});
	return json(number);
}
```

**POST:**
```js
<input
	type="text"
	autocomplete="off"
	on:keydown={async (e) => {
		if (e.key !== 'Enter') return;

		const input = e.currentTarget;
		const description = input.value;

		const response = await fetch('/todo', {
			method: 'POST',
			body: JSON.stringify({ description }),
			headers: {
				'Content-Type': 'application/json'
			}
		});

		input.value = '';
	}}
/>
```

```js
<!-- load function -->
import { json } from '@sveltejs/kit';
import * as database from '$lib/server/database.js';

export async function POST({ request, cookies }) {
	const { description } = await request.json();

	const userid = cookies.get('userid');
	const { id } = await database.createTodo({ userid, description });

	return json({ id }, { status: 201 });
}
```

Similarly, we can add handlers for other HTTP verbs (PUT and DELETE handlers).

- If don't need to return any actual data to the browser, we're returning an `empty Response` with a `204` No Content status.
(`return new Response(null, { status: 204 });`)

8. **Stores** 
**page:**
- The one you'll use most often is page, which provides information about the current page:
	- url — the URL of the current page
	- params — the current page's parameters
	- route — an object with an id property representing the current route
	- status — the HTTP status code of the current page
	- error — the error object of the current page, if any (you'll learn more about error handling in later exercises)
	- data — the data for the current page, combining the return values of all load functions
	- form — the data returned from a form action
**navigating:**
-  The value of navigation will become an object with the following properties:
	- from and to — objects with params, route and url properties
	- type — the [type](https://kit.svelte.dev/docs/types#public-types-navigation) of navigation, e.g. link, popstate or goto

```js
<script>
	import { page, navigating } from '$app/stores';
</script>
```

**updated:**
- The `updated` store contains `true` or `false` when the app has been deployed since the page was first opened.
- Must config `kit.version.pollInterval` in `config` file.

```js
<script>
	import { page, navigating, updated } from '$app/stores';
</script>

{#if $updated}
	<div class="toast">
		<p>
			A new version of the app is available

			<button on:click={() => location.reload()}>
				reload the page
			</button>
		</p>
	</div>
{/if}
```

9. **Errors and redirects**

**Basics:**
- Expected error
```js
import { error } from '@sveltejs/kit';

export function load() {
	throw error(420, 'Enhance your calm');
}
```

- Unexpected error (a bug in your app)
```js
export function load() {
	throw new Error('Kaboom!');
}
```
**Error pages:**
- When something goes wrong inside a `load` function, SvelteKit renders an error page.
- Customize error page  by creating a `src/routes/+error.svelte` component
-  We can create more granular `+error.svelte` in each routes.

**Fallback errors:**
- If things go really `wrong`,  while loading the root layout data, or while rendering the error page.
- You can customise the fallback error page. Create a `src/error.html` file:
```jsx
<h1>Game over</h1>
<p>Code %sveltekit.status%</p>
<p>%sveltekit.error.message%</p>
```
**Redirects:**
-  Use the `throw` mechanism to redirect.
```js
import { redirect } from '@sveltejs/kit';

export function load() {
	throw redirect(307, '/b');
}
```
- We can use in load functions, form actions, API routes and the handle hook.
- The most common status codes you'll use:
	- 303 — for form actions, following a successful submission
	- 307 — for temporary redirects
	- 308 — for permanent redirects

## PART 2: ADVANCE SVELTEKIT
1. **Hooks**
**handle:** → `src/hooks.server.js`
- is ways to intercept and override the framework's default behaviour.
- default:

```js
export async function handle({ event, resolve }) {
	return await resolve(event);
}
```

**The RequestEvent object:**
- The event object passed into `handle` is the same object — an instance of a `RequestEvent` that is passed into API routes, form actions, load functions.
- It contains some usefull properties and methods like: cookies, fetch, locals, params, request,...

**handleFetch:**
- be used to make credentialed requests on the server.
- make relative requests on the server
- internal requests (without the overhead of an HTTP call)

**handleError:**
- intercept unexpected errors and trigger some behaviour, like pinging a Slack channel or sending data to an error logging service.

2. **Page options**
**Basics:**
- We can also export various page options from these modules:
  - ssr — whether or not pages should be server-rendered
  - csr — whether to load the SvelteKit client
  - prerender — whether to prerender pages at build time, instead of per-request
  - trailingSlash — whether to strip, add, or ignore trailing slashes in URLs
**ssr:**
- Setting `ssr` to `false` inside your root +layout.server.js effectively turns your entire app into a single-page app (SPA).
**csr:**
- you can disable client-side rendering altogether: `export const csr = false`

**prerender:**
-  means generating HTML for a page once, at build time, rather than dynamically for each request.
-  serving static data is extremely cheap and performant
-  `export const prerender = true;`
-  Not all pages can be prerendered. Because it must be static data for everyone.
-  Setting prerender to true inside your root +layout.server.js effectively turns SvelteKit into a static site generator (SSG).

**trailingSlash**
- default, SvelteKit strips trailing slashes (default = 'nerver'). (`/foo/` same as `/foo`)
- If we want to config to `always` or `ignore`:
  ```
  export const trailingSlash = 'always';
  export const trailingSlash = 'ignore';
  ```

3. **Link options**
**Preloading:**
- 
**Reloading the page:**
- 

4. **Advance routing**
**Optional parameters:**
- 
**Rest parameters:**
- 
**Param matchers:**
- 
**Route groups:**
- 
**Breaking out of layouts:**
- 
1. **Advance loading**
**Universal load functions:**
- 
**Using both load functions:**
- 
**Using parent data:**
- 
**Invalidation:**
- 
**Custom dependencies:**
- 
**invalidateAll:**
- 
1. **Environment variables**
**$env/static/private:**
- 
**$env/dynamic/private:**
- 
**$env/static/public:**
- 
**$env/dynamic/public:**