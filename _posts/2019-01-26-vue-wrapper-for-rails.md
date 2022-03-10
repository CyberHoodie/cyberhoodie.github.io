---
layout: post
title:  "Vue wrapper for Rails"
date:   2019-01-26 12:00:00 -0500
tags: ["Today I Learned", "Vue"]
---

Normally when I want to add a Vue.js app to my Rails app (using webpacker), I will have a javascript pack with code containing something like this:

```javascript
// app/javascript/packs/application.js
import Vue from 'vue/dist/vue.esm'
import TurbolinksAdapter from 'vue-turbolinks'
import App1 from '../app1.vue'
import App2 from '../app2.vue'

Vue.use(TurbolinksAdapter)

document.addEventListener('turbolinks:load', () => {
  const element = document.querySelector("#app-1")
  if (element != undefined) {
    const app1 = new Vue({
      el: element,
      template: "<App1 />",
      components: { App1 }
    })
  }
})

document.addEventListener('turbolinks:load', () => {
  const element = document.querySelector("#app-2")
  if (element != undefined) {
    const app1 = new Vue({
      el: element,
      template: "<App2 />",
      components: { App2 }
    })
  }
})

// repeat that fat block for all apps you need to include...
```

That's way too much repetitive code for me. Luckily, thanks to Chris over at GoRails, I learned about a way to automatically bind your Vue apps to with far less code.

Replace that monstrosity above with this:

```javascript
// app/javascript/packs/application.js
import Vue from 'vue/dist/vue.esm'
import TurbolinksAdapter from 'vue-turbolinks';
import App1 from '../app1.vue'
import App2 from '../app2.vue'

Vue.use(TurbolinksAdapter)

// this is the really cool part (part 1)
Vue.component('app1', App1)
Vue.component('app2', App2)
// repeat that one line for all apps you need to include!...

document.addEventListener('turbolinks:load', () => {
  const app = new Vue({
    // this is the really cool part (part 2)
    el: '[data-behavior="vue"]',
  })
})
```

Then, in your `layout.html.erb` file, add this simple data attribute to the div surrounding your yield tag:

```erb
<div data-behavior="vue">
  <%= yield %>
</div>
```

Now, all your components defined by Vue.component are available in your erb view! You can even pass data to them like you would in the template files.

Here is an example:

```erb
<app1 :someData="<%= @some_model.to_json %>"></app1>
```

Pretty cool!
