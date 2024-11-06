---
sidebar_position: 1
---

## PART 1: BASIC SVELTE

1. if else statement

```svelte
{#if count > 10}
	<p>{count} is greater than 10</p>
{:else if count < 5}
	<p>{count} is less than 5</p>
{:else}
	<p>{count} is between 5 and 10</p>
{/if}
```

2. Each blocks

```svelte
<script>
	const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];
	let selected = colors[0];
</script>

<div>
	{#each colors as color, i }
		<button
			aria-current={selected === color}
			aria-label={color}
			style="background: {color}"
			on:click={() => selected = color}
		>{i + 1}</button>
	{/each}
</div>
```

```svelte
{#each things as thing}
	<Thing name={thing.name} />
{/each}
```

3. Await blocks
 Svelte makes it easy to await the value of promises directly in your markup:
 ```svelte
 <script>
	import { getRandomNumber } from './utils.js';

	let promise = getRandomNumber();

	function handleClick() {
		promise = getRandomNumber();
	}
</script>

<button on:click={handleClick}>
	generate random number
</button>

{#await promise}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
 ```

Omit some blocks:
```svelte
{#await  promise then number}
<p>The number is {number}
{/await}
```

4. Reactive declarations/statement. 
```svelte
let count = 0;
$: doubled = count * 2;
```

```svelte
let count = 0;

$: console.log(`the count is ${count}`);

$: if (count >= 10) {
	alert('count is dangerously high!');
	count = 0;
}
```

5. Updating arrays and objects

Svelte's reactivity is triggered by assignments
```svelte
function addNumber() {
	numbers.push(numbers.length + 1); // not do this
	numbers = [...numbers, numbers.length + 1]; // do this
}
```

Do the same with `pop, shift, unshift and splice`

→ Simple rule:  the name of the updated variable must appear on the `left hand side` of the assignment.
```svelte
const obj = { foo: { bar: 1 } };
const foo = obj.foo;
foo.bar = 2;  → won't trigger reactivity on `obj.foo.bar`
```

6. Props 
```svelte
<script>
	export let answer = 'abcd'; // default value
</script>
```

```svelte
<script>
	import Nested from './Nested.svelte';
</script>

<Nested answer={42} />

Ex:
<PackageInfo {...pkg} />  // spread props
```

7. Inline handlers
```svelte
<script>
	let m = { x: 0, y: 0 };

	// function handleMove(event) {
	// 	m.x = event.clientX;
	// 	m.y = event.clientY;
	// }
</script>

<div
	on:pointermove={(e) => {
		m = { x: e.clientX, y: e.clientY };
	}}
>
	The pointer is at {m.x} x {m.y}
</div>
```

→ Use inline handlers or not → don't affect to performance, the compiler will always do the right thing, whichever form you choose.


8. Event modifiers:
```svelte
<button on:click|once={() => alert('clicked')}>
	Click me
</button>
```
List modifiers:
- preventDefault
- stopPropagation
- passive
- nonpassive
- capture
- once
- self
- trusted
- event.isTrusted

You can chain modifiers together, e.g. `on:click|once|capture={...}`.

9. Component events
```svelte
<Inner on:message={handleMessage} />
```

```svelte
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
</script>

<button on:click={sayHello}>
	Click to say hello
</button>
```

10. Event forwarding

**App.svelte**
```svelte
<script>
	import Outer from './Outer.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

<Outer on:message={handleMessage} />
```

**Outer.svelte**
```svelte
<script>
	import Inner from './Inner.svelte';
</script>

<Inner on:message />
```


**Inner.svelte**
```svelte
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
</script>

<button on:click={sayHello}>
	Click to say hello
</button>
```

11. DOM event forwarding

```svelte
// BigRedButton.svelte
<button on:click>
	Push
</button>
```

```svelte
// App.svelte
<script>
	function handleClick(){
		// do something
	}
</script>
<BigRedButton on:click={handleClick} />
```

12. Bindings / Inputs
**Text**
```svelte
<input bind:value={name}>
<textarea bind:value={value}></textarea>
```

**Number**
```svelte
<script>
	let a = 1;
	let b = 2;
</script>

<label>
	<input type="number" bind:value={a} min="0" max="10" />
	<input type="range" bind:value={a} min="0" max="10" />
</label>

<label>
	<input type="number" bind:value={b} min="0" max="10" />
	<input type="range" bind:value={b} min="0" max="10" />
</label>

<p>{a} + {b} = {a + b}</p>
```

**Checkbox**
```svelte
<input type="checkbox" bind:checked={yes}>
```

**Select**

```svelte
<select
	bind:value={selected}
	on:change={() => answer = ''}
>
```

**Group inputs**
https://learn.svelte.dev/tutorial/group-inputs 

```svelte
<input
	type="radio"
	name="scoops"
	value={number}
	bind:group={scoops}
/>
```

Select multiple
https://learn.svelte.dev/tutorial/multiple-select-bindings 
```svelte
<h2>Flavours</h2>

<select multiple bind:value={flavours}>
	{#each ['cookies and cream', 'mint choc chip', 'raspberry ripple'] as flavour}
		<option>{flavour}</option>
	{/each}
</select>
```

13. Lifecycle
- **onMount**: runs after the component is first rendered to the DOM.
```svelte
<script>
	import { onMount } from 'svelte';

		onMount(() => {
			// do something
		})
</script>
```

- **beforeUpdate**: run before the DOM is updated.
- **afterUpdate**:  running code once the DOM is in sync with your data.
→  useful for doing things like updating the scroll position of an element.

14. tick
- Can call it any time.
- Allows for instantaneous updates to the DOM, avoiding unnecessary work and allowing the browser to batch tasks more effectively. 
- It resolves a promise as soon as pending state changes are applied, ensuring the DOM is updated without immediate DOM updates.

Example: https://learn.svelte.dev/tutorial/tick

15. Store

**Writable stores**
```js
import { writable } from 'svelte/store';

export const count = writable(0);
```

```js
// reset function
function reset() {
	count.set(0);
}

// update function
function increment() {
	count.update((n) => n + 1);
}
```

**Auto subscriptions:**
- use a store value by prefixing the store nam with `$`.
-  only works with store variables that are declared (or imported) at the top-level scope of a component.
-  can use it anywhere in the `<script>` as well, such as in event handlers or reactive declarations.
-  Any name beginning with `$` is assumed to refer to a store value.
-  Svelte will prevent you from declaring your own variables with a `$` prefix.

**Readable stores:**
- Stores representing mouse position or user geolocation should not be writable by anyone. 
- To set these values, use readable stores.js. 
- Initial value is an initial value, while start function takes a set callback and returns a stop function. -
- Stop is called when subscriber unsubscribes.

```js
import { readable } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
	const interval = setInterval(() => {
		set(new Date());
	}, 1000);

	return function stop() {
		clearInterval(interval);
	};
});
```

**Derived stores:**
- A store can be created with derived value, based on the value of other stores
Example:

```js
import { readable, derived } from 'svelte/store';

// time store
export const time = readable(new Date(), function start(set) {
	const interval = setInterval(() => {
		set(new Date());
	}, 1000);

	return function stop() {
		clearInterval(interval);
	};
});

const start = new Date();

// elapsed store
export const elapsed = derived(
	time,
	($time) => Math.round(($time - start) / 1000)
);
```

**Custom stores:**
```js
import { writable } from 'svelte/store';

export function createTodoStore(initial) {
	let uid = 1;

	const todos = initial.map(({ done, description }) => {
		return {
			id: uid++,
			done,
			description
		};
	});

	const { subscribe, update } = writable(todos);

	return {
		subscribe,
		add: description => {
			const todo = {
				id: uid++,
				done: false,
				description
			};

			update($todos => [...$todos, todo])
		},
		remove: todo => {
			update($todos => $todos.filter((t) => t !== todo));
		},
		mark: (todo, done) => {
			update($todos => [
				...$todos.filter((t) => t !== todo),
				{ ...todo, done }
			]);
		}
	};
}

```

16: Store binding
```svelte
<input bind:value={$name}>
```
→ Changing the input value will now update name and all its dependents.

```svelte
<button on:click={() => $name += '!'}>
	Add exclamation mark!
</button>
```

The `$name += '!' assignment` is equivalent to `name.set($name + '!')`.


## PART 2: ADVANCED SVELTE
1. Motion

Tweens:
```svelte
<script>
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';

	const progress = tweened(0, {
		duration: 400,
		easing: cubicOut
	});
</script>
```

**Springs**
The spring function is an alternative to tweened that often works better for values that are frequently changing.

```svelte
<script>
	import { spring } from 'svelte/motion';

	let coords = spring({ x: 50, y: 50 }, {
	stiffness: 0.1,
	damping: 0.25
});
	let size = spring(10);
</script>
```
2. Transition:
- Transitioning elements `into and out` of the DOM. →  makes this very easy with the `transition` directive.
```svelte
<script>
	import { fade } from 'svelte/transition';
	let visible = true;
</script>

<p transition:fade>
	Fades in and out
</p>
```

- Transition functions can accept parameters. Replace the `fade` transition with `fly`.
- The transition is reversible, transitioning from the `current point` if the checkbox is toggled during its ongoing operation.

- An element can have an `in` or an `out` directive:

```svelte
<p in:fly={{ y: 200, duration: 2000 }} out:fade>
	Flies in, fades out
</p>
```

Custom CSS transition: https://learn.svelte.dev/tutorial/custom-css-transitions
Custom Js transition: example for typewriter effect: https://learn.svelte.dev/tutorial/custom-js-transitions

**Transition events:**
- Selve support events from transition start until end.

```svelte
<p
	transition:fly={{ y: 200, duration: 2000 }}
	on:introstart={() => status = 'intro started'}
	on:outrostart={() => status = 'outro started'}
	on:introend={() => status = 'intro ended'}
	on:outroend={() => status = 'outro ended'}
>
	Flies in and out
</p>
```

**Global transitions:**
```svelte
<div transition:slide|global>
	{item}
</div>
```
In Svelte 3, transitions were global by default and you had to use the |local modifier to make them local.

**Key blocks:**
- Play elememt transition whenever a value changes instead of only when the element enters or leaves the DOM.
- Message is updated by i variable → wrap the p element in a key block

```svelte
{#key i}
	<p in:typewriter={{ speed: 10 }}>
		{messages[i] || ''}
	</p>
{/key}
```

**Deferred transitions:**
- Svelte's transition engine allows deferring transitions between multiple elements, enhancing user understanding.
- This is achieved through the crossfade function in transition.js, which creates send and receive transitions. 

```svelte
<script>
	import { send, receive } from './transition.js';

	export let store;
	export let done;
</script>

<li
	class:done
	in:receive={{ key: todo.id }}
	out:send={{ key: todo.id }}
>
```

3. Animation

**The animate directive**

We need to apply motion to the elements that aren't transitioning
```svelte
<script>
	import { flip } from 'svelte/animate';
	import { send, receive } from './transition.js';

	export let store;
	export let done;
</script>

<li
	class:done
	in:receive={{ key: todo.id }}
	out:send={{ key: todo.id }}
	animate:flip={{ duration: 200 }}
>
```

4. Actions

**The use directive**
```svelte
// focus when open menu
<script>
	import { trapFocus } from './actions.js';
</script>
<div class="menu" use:trapFocus>
```
**Adding parameters**
using the Tippy.js library → handle show tooltip
https://learn.svelte.dev/tutorial/adding-parameters-to-actions

5. Advanced binding
**Contenteditable binding**

```html
<div bind:innerHTML={html} contenteditable />
```

**Each block bindings**
You can even bind to properties inside an `each` block.

```svelte
{#each todos as todo}
	<li class:done={todo.done}>
		<input
			type="checkbox"
			bind:checked={todo.done}
		/>

		<input
			type="text"
			placeholder="What needs to be done?"
			bind:value={todo.text}
		/>
	</li>
{/each}
```

**Media elements**
https://learn.svelte.dev/tutorial/media-elements

**Dimensions:**
Every block-level element has `clientWidth`, `clientHeight`, `offsetWidth` and `offsetHeight` bindings.
```svelte
<div bind:clientWidth={w} bind:clientHeight={h}>
	<span style="font-size: {size}px" contenteditable>{text}</span>
	<span class="size">{w} x {h}px</span>
</div>
```

- These bindings are readonly — changing the values of `w` and `h` won't have any effect on the element.
- not recommended to use this for large numbers of elements.
- `display: inline` elements cannot be measured with this approach;

**this**

```svelte
<canvas
	bind:this={canvas}
	width={32}
	height={32}
></canvas>
```

Note that the value of `canvas` will be `undefined` until the component has mounted.

**Component bindings**
-  You can bind to component props.
```svelte
<Keypad
	bind:value={pin}
	on:submit={handleSubmit}
/>
```

```svelte
// Keypad.svelte
<script>
	export let value = '';
	// do something to change the value
</script>
```

**Binding to component instances:**
-  You can bind to component instances themselves with bind:this.
```svelte
<Canvas bind:this={canvas} color={selected} size={size} />
```

6. Classes and styles
Directive:
```svelte
<button
	class="card"
	class:flipped={flipped}
	on:click={() => flipped = !flipped}
>
```

This directive means 'add the flipped class whenever flipped is truthy'.

If the name of the class is the same as the name of the value it depends on:

```svelte
<button
	class="card"
	class:flipped
	on:click={() => flipped = !flipped}
>
```
You can write your inline style attributes literally:
```svelte
<button
	class="card"
	style="transform: {flipped ? 'rotateY(0)' : ''}; --bg-1: palegoldenrod; --bg-2: black; --bg-3: goldenrod"
	on:click={() => flipped = !flipped}
>
```

We can tidy things up by using the `style:` directive:
```svelte
<button
	class="card"
	style:transform={flipped ? 'rotateY(0)' : ''}
	style:--bg-1="palegoldenrod"
	style:--bg-2="black"
	style:--bg-3="goldenrod"
	on:click={() => flipped = !flipped}
>
```

Component styles:

```svelte
<div class="boxes">
	<Box --color="red" />
	<Box --color="green" />
	<Box --color="blue" />
</div>
```

```svelte
<style>
	.box {
		width: 5em;
		height: 5em;
		border-radius: 0.5em;
		margin: 0 0 1em 0;
		background-color: var(--color, #ddd);
	}
</style>
```


7. Component composition:

**Slot**

```svelte
<div class="card">
	<slot />
</div>

<Card>
	<span>Patrick BATEMAN</span>
	<span>Vice President</span>
</Card>
```

**Named slots:**

```svelte
<div class="card">
	<header>
		<slot name="telephone" />
		<slot name="company" />
	</header>

	<slot />
		
	<footer>
		<slot name="address" />
	</footer>
</div>
```

```svelte
<Card>
	<span>Patrick BATEMAN</span>
	<span>Vice President</span>
	<span slot="telephone">212 555 6342</span>
	<span slot="company">
		Pierce &amp; Pierce
		<small>Mergers and Aquisitions</small>
	</span>
	<span slot="address">358 Exchange Place, New York, N.Y. 100099 fax 212 555 6390 telex 10 4534</span>
</Card>
```

Slot fallback
```svelte
<header>
	<slot name="telephone">
		<i>(telephone)</i>
	</slot>
		
	<slot name="company">
		<i>(company name)</i>
	</slot>
</header>
```

Slot props 
```svelte
<FilterableList
	data={colors}
	field="name"
	let:item={row}
>

<div class="content">
	{#each data.filter(matches) as item}
		<slot {item} />
	{/each}
</div>
```

Checking for slot content
```svelte
{#if $$slots.header}
	<div class="header">
		<slot name="header"/>
	</div>
{/if}
```

8. Context API
**setContext and getContext**

Parent component:
```js
	setContext('canvas', {
		addItem
	});
```

Child component
```js
	getContext('canvas').addItem(draw);
```


Your context object can include anything, including stores.
```js
// in a parent component
import { setContext } from 'svelte';
import { writable } from 'svelte/store';

setContext('my-context', {
	count: writable(0)
});
```

```js
// in a child component
import { getContext } from 'svelte';

const { count } = getContext('my-context');

$: console.log({ count: $count });
```

9. **Special elements:**

**`<svelte:self>`**

A module can't import itself. Instead, we use `<svelte:self>`:
```svelte
{#if file.files}
	<svelte:self {...file}/>
{:else}
	<File {...file}/>
{/if}
```

**`<svelte:component>`**
```svelte
<select bind:value={selected}>
	{#each options as option}
		<option value={option}>{option.color}</option>
	{/each}
</select>

<svelte:component this={selected.component} />
```

The `this` value can be any component constructor, or a falsy value — if it's falsy, no component is rendered.


**`<svelte:element>`**
 we don't always know in advance what kind of DOM element to render, so, use `<svelte:element` to dynamic render html tags:
 ```svelte
 <svelte:element this={selected}>
	I'm a <code>&lt;{selected}&gt;</code> element
</svelte:element>
```

**`<svelte:window>`**

- Add event listeners to any DOM element, you can add event listeners to the window object with `<svelte:window>`

```svelte
<svelte:window on:keydown={handleKeydown} />
```

We can also bind to certain properties of window, such as scrollY:
```svelte
<svelte:window bind:scrollY={y} />
```

The list of properties you can bind to is as follows:
- innerWidth
- innerHeight
- outerWidth
- outerHeight
- scrollX
- scrollY
- online — an alias for window.navigator.onLine

All except `scrollX` and `scrollY` are readonly.

**`<svelte:body>`**

`<svelte:body>` element allows you to listen for events that fire on `document.body`.

→ useful with the `mouseenter` and `mouseleave` events.
```svelte
<svelte:body
	on:mouseenter={() => hereKitty = true}
	on:mouseleave={() => hereKitty = false}
/>
```

**`<svelte:document>`**

- useful with events like `selectionchange`:
```svelte
<svelte:document on:selectionchange={handleSelectionChange} />
```
*Note:* Avoid `mouseenter` and `mouseleave` handlers on this element. Use `<svelte:body>` instead.

**`<svelte:head>`**
-  Allows you to insert elements inside the `<head>` of your document. 
- Useful for things like `<title>` and `<meta>` tags, good for SEO.

Example dynamic css style:
```svelte
<script>
	const themes = ['margaritaville', 'retrowave', 'spaaaaace', 'halloween'];
	let selected = themes[0];
</script>

<svelte:head>
	<link rel="stylesheet" href="/stylesheets/{selected}.css" />
</svelte:head>

<h1>Welcome to my site!</h1>

<select bind:value={selected}>
	<option disabled>choose a theme</option>

	{#each themes as theme}
		<option>{theme}</option>
	{/each}
</select>
```

**`<svelte:options>`**
- Allows you to specify compiler options.
- Prevent flash issue when component updated (receive new data).
Add this to the top of `Component`:
```svelte
<svelte:options immutable={true} />
```
More: https://learn.svelte.dev/tutorial/svelte-options

**`<svelte:fragment>`**
- Allows you to place content in a named slot without wrapping it in a container DOM element.
```svelte
<svelte:fragment slot="game">
	{#each squares as square, i}
		<button
			class="square"
			class:winning={winningLine?.includes(i)}
			disabled={square}
			on:click={() => {
				squares[i] = next;
				next = next === 'x' ? 'o' : 'x';
			}}
		>
			{square}
		</button>
	{/each}
</svelte:fragment>
```


10. **Module Context **
**Sharing code:**
- to run some code outside of an individual component instance. 
- to do this, declaring a `<script context="module">` block.
```svelte
<script context="module">
	let current;
</script>
```
**Export:**
- Anything exported from a `context="module"` script block becomes an export from the module itself.
- You can import and use when need.

11. **Miscellaneous**
The @debug tag
- to pause execution, use the `{@debug ...}` tag with a comma-separated list of values you want to inspect.
```svelte
{@debug user}

<h1>Hello {user.firstname}!</h1>
```