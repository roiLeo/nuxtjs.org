---
title: Context and Helpers
description: The context provides *additional* and often optional information about the current request to the application.
position: 2
category: Concepts
categoryPosition: 2
questions:
  - question: What is the reason the context exists?
    answers:
      - Server-side rendering
      - Having global state
      - Laziness
    correctAnswer: Server-side rendering
  - question: Which key is not in the context?
    answers:
      - env
      - isDev
      - $store
    correctAnswer: $store
  - question: Which context key is only available on the *server* side?
    answers:
      - from
      - app
      - req
    correctAnswer: req
  - question: Which context key is only available on the *client* side?
    answers:
      - from
      - res
      - app
    correctAnswer: from
  - question: What can the `$nuxt` helper *not* do?
    answers:
      - Displaying the version of Nuxt
      - Providing info about the users internet connection status
      - Accessing exposed module functions
    correctAnswer: Displaying the version of Nuxt
  - question: What are the names of the process helpers
    answers:
      - global, client and server
      - server, client and static
      - ssr, spa and static
    correctAnswer: server, client and static
---

![nuxt-js-context-keys](https://user-images.githubusercontent.com/13063165/83777134-5bda8780-a689-11ea-8c50-bbee8dc75714.jpeg)

The `context` object is available in specific Nuxt functions like [asyncData](https://nuxtjs.org/api), [plugins](https://nuxtjs.org/guide/plugins), [middleware](https://nuxtjs.org/guide/routing#middleware) and [nuxtServerInit](https://nuxtjs.org/guide/vuex-store#the-nuxtserverinit-action). It provides *additional* and often optional information about the current request to the application.

First and foremost, the context is used to provide access to other parts of the Nuxt.js application, e.g. the Vuex store or the underlying `connect` instance. Thus, we have the `req` and `res` objects in the context available on the server side and `store` always available. But with time, the context was extended with many other helpful variables and shortcuts. Now we have access to HMR functionalities in `development` mode, the current `route`, page `params` and `query`, as well as the option to access environment variables through the context. Furthermore, module functions and helpers can be exposed through the context to be available on both - the client and the server side.

**All context keys that are present by default**

```js
function (context) { // Could be asyncData, nuxtServerInit, ...
  // Always available
  const {
    app,
    store,
    route,
    params,
    query,
    env,
    isDev,
    isHMR,
    redirect,
    error
  } = context
  
  // Only available on the Server-side
  if (process.server) {
    const { req, res, beforeNuxtRender } = context
  }
  
  // Only available on the Client-side
  if (process.client) {
    const { from, nuxtState } = context
  }
}
```

**Important:** The *context* we refer to here is not to be confused with the `context` object available in `[Vuex Actions](https://vuex.vuejs.org/guide/actions.html)` or the one available in the `build.extend` function in your `nuxt.config.js`. These are all not related to each other!

Learn more about the different context keys in our Glossary

## Examples

### Using page parameters for your API query

The context directly exposes possible dynamic parameters of the route via `context.params`. In the following example, we call an API via the `nuxt/http` module using a dynamic page parameter as part of the URL. Modules, like the `nuxt/http` module, can expose own functions which are then available through the `[context.app](http://context.app)` object.

Also, we wrap the API call in a `try/catch` statement to handle potential errors. With the `context.error` function, we can directly show Nuxt's error page and pass in the occurred error.

`pages/posts/_id.vue`

```js
export default {
  async asyncData (context) {
    const id = context.params.id
    try { 
		  // Using the nuxtjs/http module here exposed via context.app
	    const post = await context.app.$http.$get(`https://api.nuxtjs.dev/posts/${id}`)
      return { post }
    } 
		catch (error) {
      context.error(error) // Show the nuxt error page with the thrown error
    }
  }
}
```

With [ES6](https://hacks.mozilla.org/2015/05/es6-in-depth-destructuring/) we can use this syntax to descructure our context object. We can pass in the objects we want to have access to and then we can use them in the code without using the word context.

`pages/posts/_id.vue`

```js
export default {
  async asyncData ({params, $http, error}) {
    const id = params.id
    
    try { 
		  // Using the nuxtjs/http module here exposed via context.app
	    const post = await $http.$get(`https://api.nuxtjs.dev/posts/${id}`)
      return { post }
    } 
		catch (error) {
      error(error) // Show the nuxt error page with the thrown error
    }
  }
}
```

Want to use query parameters instead? You then use `[context.query.id](http://context.query.id)` then.

### Redirecting users & accessing the store

Accessing the Vuex store (when you have it set up through the `store` directory) is also possible through the context. It provides a `store` object which can be treated as `this.$store` in Vue components.
In addition, we use the `redirect` method, an exposed helper through the context, to redirect the user in case the `authenticated` state is falsy.

```js
export default {
  middleware ({ store, redirect }) { // retrieving keys via object destructuring
    const isAuthenticated = store.state.authenticated
    if (!isAuthenticated) {
      return redirect('/login')
    }
  }
}
```

# Helpers

Besides the shortcuts in the context, there are also other tiny helpers present in your Nuxt.js application.

# $nuxt: The Nuxt.js helper

`$nuxt` is a helper designed to improve the user experience and to be an escape hatch in some situations. It is accessible via `this.$nuxt` in Vue components and via `window.$nuxt` otherwise on the client side.

### Connection checker

The `$nuxt` helper provides a quick way to find out whether the internet connection of a user is present or not: It exposes the boolean values `isOffline` and `isOnline`. We can use these to show a message as soon as the user is offline (for example).

Example in your default layout ( `layouts/default.vue`)

```html
<template>
  <div>
    <div v-if="$nuxt.isOffline">You are offline</div>
    <Nuxt/>
  </div>
</template>
```

### Accessing the root instance

Besides providing DX/UX features, the `$nuxt` helper also provides a shortcut to the root instance of you application from every other component. But that's not everything — you can also access the `$nuxt` helper through `window.$nuxt` which can be used as an escape hatch to gain access to module methods like `$axios` from outside your Vue components. Anyway, this should be used wisely and **only as last resort**.

### Refreshing page data

When you want to refresh the current page for the user, you don't want to fully reload the page because you might hit the server again and at least re-initialize the whole Nuxt.js application. Instead, you often only want to refresh the data, provided by `asyncData` or `fetch`.

You can do so, by using `this.$nuxt.refresh()`!

```js
<template>
 <div>
   <div>{{ content }}</div>
   <button @click="refresh">Refresh</button>
 </div>
</template>

<script>
export default {
  asyncData () {
   return { content: 'Created at: ' + new Date() }
  },
  methods: {
    refresh () {
      this.$nuxt.refresh()
    }
  }
}
</script>
```

### Controlling the loading bar

With `$nuxt`, you can also control Nuxt's loading bar programmatically via `this.$nuxt.$loading`.

```js
export default {
  mounted () {
    this.$nextTick(() => {
      this.$nuxt.$loading.start()
      setTimeout(() => this.$nuxt.$loading.finish(), 500)
    })
  }
}
```

Read more in the corresponding loading feature chapter

## onNuxtReady helper

If you want to run some scripts *after* your Nuxt.js application has been loaded and is ready, you can use the `window.onNuxtReady` function. This can be useful when you want to execute a function on the client-side without increasing the time to interactive of your site.

```js
window.onNuxtReady(() => {
  console.log('Nuxt.js is ready and mounted')
})
```

## Process helpers

Nuxt.js injects three boolean values into the global `process` object which will help you to determine whether your app was rendered on the server or fully on the client, as well as checking for static site generation. These helpers are available across your application and are commonly used in `asyncData` userland code.

`pages/about.vue`

```js
<template>
  <h1>I am rendered on the {{ renderedOn }} side </h1>
</template>

<script>
export default {
  asyncData () {
    return { renderedOn: process.client ? 'client' : 'server' }
  }
}
</script>
```

In the example, `renderedOn` will evaluate to `'server'` when using server-side rendering and a user accesses the page directly. When the user would navigate to the page from another part of the application, e.g. by click on a `<NuxtLink>`, it will evaluate to client.

## Quiz

<quiz :questions="questions"></quiz>