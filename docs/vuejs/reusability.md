---
sidebar_position: 5
---

# VUE 3 - Reusability

## Composables
#### What is a "Composable"?
- A "composable" is a function that leverages Vue's Composition API to encapsulate and reuse **stateful logic**.
#### Mouse Tracker Example
```ts
// mouse.js
import { ref, onMounted, onUnmounted } from 'vue'

// by convention, composable function names start with "use"
export function useMouse() {
  // state encapsulated and managed by the composable
  const x = ref(0)
  const y = ref(0)

  // a composable can update its managed state over time.
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // a composable can also hook into its owner component's
  // lifecycle to setup and teardown side effects.
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // expose managed state as return value
  return { x, y }
}
```

And this is how it can be used in components:
```ts
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```
- One composable function can call one or more other composable functions → compose complex logic.
```ts
// event.js
import { onMounted, onUnmounted } from 'vue'

export function useEventListener(target, event, callback) {
  // if you want, you can also make this
  // support selector strings as target
  onMounted(() => target.addEventListener(event, callback))
  onUnmounted(() => target.removeEventListener(event, callback))
}
```
```ts
// mouse.js
import { ref } from 'vue'
import { useEventListener } from './event'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  useEventListener(window, 'mousemove', (event) => {
    x.value = event.pageX
    y.value = event.pageY
  })

  return { x, y }
}
```

#### Async State Example
When doing async data fetching, we often need to handle different states: loading, success, and error:
```ts
// fetch.js
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  fetch(url)
    .then((res) => res.json())
    .then((json) => (data.value = json))
    .catch((err) => (error.value = err))

  return { data, error }
}
```
```ts
<script setup>
import { useFetch } from './fetch.js'

const { data, error } = useFetch('...')
</script>
```
We want to re-fetch whenever the URL changes, we can achieve that by also accepting refs as an argument:
```ts
// fetch.js
import { ref, isRef, unref, watchEffect } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  function doFetch() {
    // reset state before fetching..
    data.value = null
    error.value = null
    // unref() unwraps potential refs
    fetch(unref(url))
      .then((res) => res.json())
      .then((json) => (data.value = json))
      .catch((err) => (error.value = err))
  }

  if (isRef(url)) {
    // setup reactive re-fetch if input URL is a ref
    watchEffect(doFetch)
  } else {
    // otherwise, just fetch once
    // and avoid the overhead of a watcher
    doFetch()
  }

  return { data, error }
}
```
#### Conventions and Best Practices
**Naming:**
- naming with camelCase names that start with "use".

**Input Arguments:**
```ts
import { unref } from 'vue'

function useFeature(maybeRef) {
  // if maybeRef is indeed a ref, its .value will be returned
  // otherwise, maybeRef is returned as-is
  const value = unref(maybeRef)
}
```

**Return Values:**
- The recommended convention is for composables to always return a plain, non-reactive object containing multiple refs → allows it to be destructured in components while retaining reactivity.
```ts
 // x and y are refs
const { x, y } = useMouse()
```
- Returning a reactive object from a composable → lose the reactivity connection to the state inside the composable, while the refs will retain that connection.

If you prefer to use returned state from composables as object properties, you can wrap the returned object with reactive() so that the refs are unwrapped. For example:
```ts
const mouse = reactive(useMouse())
// mouse.x is linked to original ref
console.log(mouse.x)
Mouse position is at: {{ mouse.x }}, {{ mouse.y }}
```
**Side Effects:**
When perform side effects (adding DOM event listeners or fetching data), pay attention to the following rules:
- With SSR, make sure to perform DOM-specific side effects in post-mount lifecycle hooks (`onMounted` → only called in the browser, ensure code has access to the DOM)
- Clean up side effects in `onUnmounted()` (e.g.: clean DOM event listener).

**Usage Restrictions:**
- Should only be called synchronously in `<script setup>` or the `setup()` hook.
- Access to an active component instance is necessary so that:
  - Lifecycle hooks can be registered to it.
  - Computed properties and watchers can be linked to it, so that they can be disposed when the instance is unmounted to prevent memory leaks.
#### Extracting Composables for Code Organization
Composables can be extracted not only for reuse, but also for code organization.
Composition API gives you the full flexibility to organize your component code into smaller functions based on logical concerns:
```ts
<script setup>
import { useFeatureA } from './featureA.js'
import { useFeatureB } from './featureB.js'
import { useFeatureC } from './featureC.js'

const { foo, bar } = useFeatureA()
const { baz } = useFeatureB(foo)
const { qux } = useFeatureC(baz)
</script>
```
#### Using Composables in Options API
https://vuejs.org/guide/reusability/composables.html#using-composables-in-options-api 
#### Comparisons with Other Techniques
https://vuejs.org/guide/reusability/composables.html#comparisons-with-other-techniques

## Custom Directives
#### Introduction
- Vue also allows you to register your own custom directives (like `v-modal`, `v-show`,...)
- We have introduced two forms of code reuse in Vue: components and composables.
- Here is an example of a directive that focuses an input when the element is inserted into the DOM by Vue:
```ts
<script setup>
// enables v-focus in templates
const vFocus = {
  mounted: (el) => el.focus()
}
</script>

<template>
  <input v-focus />
</template>
```
- In `<script setup>`, any camelCase variable that starts with the v prefix can be used as a custom directive.
- It is also common to globally register custom directives at the app level:
```ts
const app = createApp({})

// make v-focus usable in all components
app.directive('focus', {
  /* ... */
})
```
#### Directive Hooks
A directive definition object can provide several hook functions (all optional):
```ts
const myDirective = {
  // called before bound element's attributes
  // or event listeners are applied
  created(el, binding, vnode, prevVnode) {
    // see below for details on arguments
  },
  // called right before the element is inserted into the DOM.
  beforeMount(el, binding, vnode, prevVnode) {},
  // called when the bound element's parent component
  // and all its children are mounted.
  mounted(el, binding, vnode, prevVnode) {},
  // called before the parent component is updated
  beforeUpdate(el, binding, vnode, prevVnode) {},
  // called after the parent component and
  // all of its children have updated
  updated(el, binding, vnode, prevVnode) {},
  // called before the parent component is unmounted
  beforeUnmount(el, binding, vnode, prevVnode) {},
  // called when the parent component is unmounted
  unmounted(el, binding, vnode, prevVnode) {}
}
```
```ts
<div v-example:foo.bar="baz">
```
The binding argument would be an object in the shape of:
```ts
{
  arg: 'foo',
  modifiers: { bar: true },
  value: /* value of `baz` */,
  oldValue: /* value of `baz` from previous update */
}
```
#### Function Shorthand
It's common for a custom directive to have the same behavior for mounted and updated:
```ts
<div v-color="color"></div>

app.directive('color', (el, binding) => {
  // this will be called for both `mounted` and `updated`
  el.style.color = binding.value
})
```
#### Object Literals
If your directive needs multiple values, you can also pass in a JavaScript object literal.
```ts
<div v-demo="{ color: 'white', text: 'hello!' }"></div>


app.directive('demo', (el, binding) => {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text) // => "hello!"
})
```
#### Usage on Components 
When used on components, custom directives will always apply to a component's root node.
```html
<MyComponent v-demo="test" />
```
It is not recommended to use custom directives on components.
## Plugins
#### Introduction
Plugins are self-contained code that usually add app-level functionality to Vue.
```js
import { createApp } from 'vue'

const app = createApp({})

app.use(myPlugin, {
  /* optional options */
})
```
```js
const myPlugin = {
  install(app, options) {
    // configure the app
  }
}
```
Exposes an `install()` method, or simply a function that acts as the install function itself.
Common scenarios where plugins are useful include:
- Register one or more global components or custom directives with `app.component()` and `app.directive()`.
- Make a resource injectable throughout the app by calling `app.provide()`.
- Add some global instance properties or methods by attaching them to `app.config`.`globalProperties`.
- A library that needs to perform some combination of the above (e.g. vue-router).
#### Writing a Plugin
- We will create a very simplified version of a plugin that displays `i18n` (short for `Internationalization`) strings.
This is the intended usage in templates:
```html
<h1>{{ $translate('greetings.hello') }}</h1>
```
Since this function should be globally available in all templates, we will attach it to `app.config.globalProperties` in our plugin:
```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    // inject a globally available $translate() method
    app.config.globalProperties.$translate = (key) => {
      // retrieve a nested property in `options`
      // using `key` as the path
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, options)
    }
  }
}
```
```js
import i18nPlugin from './plugins/i18n'

app.use(i18nPlugin, {
  greetings: {
    hello: 'Bonjour!'
  }
})
```
**Provide / Inject with Plugins**
Plugins also allow us to use `inject` to provide a function or attribute to the plugin's users.
```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    app.config.globalProperties.$translate = (key) => {
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, options)
    }

    app.provide('i18n', options)
  }
}
```
```ts
<script setup>
import { inject } from 'vue'

const i18n = inject('i18n')

console.log(i18n.greetings.hello)
</script>
```
