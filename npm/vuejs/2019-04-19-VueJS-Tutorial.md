---
layout: post
author: Wouter Van Schandevijl
title:  "Vue.js Tutorial"
date:   2019-04-19
desc: >
  Learning Vue.js basics with mostly code examples.
bigimg:
  url: vue-tutorial-big.png
  desc: Photo by Jacky Lo
  origin: https://unsplash.com/photos/LjX-m_UXQGM
img:
  url: vue-tutorial.jpg
  desc: "What a Vue!"
  origin: https://unsplash.com/photos/-GwHS2piM_U
imgtitle: Photo by Fezbot2000
categories: javascript
tags: [tutorial]
extras:
  - githubproject: https://github.com/itenium-be/Vue.js-Tutorial
    githubtext: "Project used for itenium Vue.js technical session"
interesting:
  - url: https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd?hl=en
    desc: "Vue.js devtools Chrome extension"
  - url: https://marketplace.visualstudio.com/items?itemName=octref.vetur
    desc: "Vetur Visual Studio Code extension"
toc:
  title: 🎑 Vue.js
package-versions:
  - package: vue
    version: 2.6.6
  - package: vue-router
    version: 3.0.1
  - package: vuex
    version: 3.0.1
  - package: vue-cli
    version: 3.5.3
---

{% include github-stars.html url="vuejs/vue" desc="Vue.js is a progressive, incrementally-adoptable JavaScript framework for building UI on the web." %}

#### Why Vue

- `100k+ ⭐`: So many people can't be wrong
- Declarative rendering with a cool, terse syntax
- Low learning curve
- Reactive and composable view components
- Optional official `vue-router` and `vuex` (statemanagement)
- `@vue/cli`: Scaffold project with optional support for TypeScript, PWA, CSS Pre-processors, Linters, Tests, ...
- Automatic dependency tracking
- Virtual DOM
- Supports IE9+

<!--more-->


#### Official Projects
{% include github-stars.html url="vuejs/vue-router" desc="The official router for Vue.js. " %}
{% include github-stars.html url="vuejs/vuex" desc="Centralized State Management for Vue.js." %}
{% include github-stars.html url="vuejs/vue-cli" desc="Standard Tooling for Vue.js Development" %}
{% include github-stars.html url="vuejs/vue-devtools" desc="⚙ Chrome and Firefox devtools extension for debugging Vue.js applications." %}
{% include github-stars.html url="vuejs/vetur" desc="Vue tooling for VS Code." %}





# Hello Vue

[HelloVue.html source]({{ 'assets/blog-assets/vue-HelloVue.html' | relative_url}})
{: .title-url}

#### Html
```html
{% raw %}<script src="https://unpkg.com/vue"></script>

<div id="app">
  <!-- Binding -->
  <h1>{{ product }}</h1>


  <!-- v-bind directive -->
  <img v-bind:src="image" :title="imageTitle">
  <!-- :src, :alt, :title, :class, :style, :disabled -->


  <!-- Conditionals -->
  <div>
    <!-- Falsy will not include in DOM -->
    <p v-if="inventory > 10">In Stock</p>
    <p v-else-if="inventory">Almost sold out</p>
    <p v-else>Out of Stock</p>

    <!-- Render with display: none -->
    <p v-show="inventory < 0">Wuuk?</p>
  </div>


  <!-- Events -->
  <!-- v-on directive shorthand is @ -->
  <button
    v-on:click="cart++; inventory--"
    :disabled="!inventory"
    :class="inventory ? '' : 'disabledButton'"
  >
    Add to Cart
  </button>
  <div class="cart">{{cart}}</div>


  <!-- Loops -->
  <div v-for="variant in variants"
    :key="variant.id"
    :style="{backgroundColor: variant.color}"
    @mouseover="setImage(variant.color, $event)"
  ></div>
</div>{% endraw %}
```

#### JavaScript
```javascript
const vm = new Vue({
    el: '#app',
    data: {
      product: 'Socks',
      image: './assets/socks-green.jpg',
      imageTitle: 'Green Socks',
      variants: [
        {id: 1, color: 'green'},
        {id: 2, color: 'blue'}
      ],
      inventory: 3,
      cart: 0
    },
    methods: {
      addToCart() {
        this.cart++;
        this.inventory--;
      },
      setImage(color, event) {
        console.log('Vue instance with data and methods', this, event);
        this.image = `./assets/socks-${color}.jpg`;
      }
    }
});
console.log('Modify data directly from the console with `vm.product = "Shoes";`');
```


# Templates

[Interpolations guide](https://vuejs.org/guide/essentials/template-syntax.html#text-interpolation)
{: .title-url}

#### Displaying values:  
```html
{% raw %}<h1>{{ name.toUpperCase() }}</h1>
<p v-text="name"></p>
<p v-html="rawHtml"></p>{% endraw %}
```



#### Computed Properties
Prefer `computed` properties over doing string formatting in the templates directly
as they will only be re-evaluated when a relevant dependency changes.

[Computed Properties guide](https://vuejs.org/guide/essentials/computed.html#computed-properties)
{: .title-url}
Usage computed property: `{% raw %}<h1>{{ title }}</h1>{% endraw %}`:

```
new Vue({
    data: {name: 'Vuer'},
    computed: {
        title() {
            return this.name.toUpperCase();
        }
    }
});
```


## Attributes

[Attributes guide](https://vuejs.org/api/built-in-directives.html#v-bind)
{: .title-url}
Binding to attribute with `v-bind` directive:  
```html
<img v-bind:src="val">
<img v-bind:[evaluated]="val">
<img :src="val">
```

Evaluated should return a bindable property or `null`.

Shorthands: `:src`, `:alt`, `:title`, `:class`, `:style`, `:disabled`

#### :style
```html
:style="{fontSize: '13px'}"
:style="{'font-size': styles.size}"
:style="styleObject"
:style="[obj1, obj2]"
```

- Use camelCase or kebab-case (quoted)
- Vendor prefixes are added automatically
- Can combine html `style` and Vue `:style` attributes.

#### :class
```html
:class="'btn' + btn.type"
:class="[cond ? 'active' : '']"
:class="{active: cond}"
```


[Conditional Rendering guide](https://vuejs.org/guide/essentials/conditional.html#conditional-rendering)
{: .title-url}
## Conditions
```html
{% raw %}<!-- Falsy values will not render -->
<p v-if="inventory > 10">In Stock</p>
<p v-else-if="inventory">Almost sold out</p>
<p v-else>Out of Stock</p>

<!-- Render a block of multiple elements -->
<!-- The template tag itself will not be rendered -->
<template v-if="cond">
    <h1>Title</h1>
    <div>...</div>
</template>

<!-- Falsy values render with display: none -->
<!-- Less DOM changes === better performance -->
<!-- There is no v-else-show and it does not work with templates -->
<p v-show="inventory">In Stock</p>{% endraw %}
```

`v-show`  
- Use when you need to toggle something often
- Always rendered: Costlier initial rendering

`v-if`  
- Use when the condition is unlikely to change
- Rendered only when the condition is true

`v-once`: Will never be updated after initial render.





[Lists guide](https://vuejs.org/guide/essentials/list.html#list-rendering)
{: .title-url}
## Looping
```html
{% raw %}<!-- Render 5 stars -->
<i v-for="i in 5" class="fa fa-star" :key="i"></i>

<!-- Loop through an Array -->
<li v-for="item in ['Gold', 'Silver', 'Bronze']" :key="item">{{ item }}</li>
<li v-for="(item, index) in [1, 2, 3]" :key="item">{{ index + ' ' + item }}</li>

<!-- Loop through an Object -->
<li v-for="(value, key) in {a: 1}" :key="key">{{ value }}</li>
<li v-for="(value, key, index) in {a: 1}" :key="key">{{ value }}</li>{% endraw %}
```

Like `v-if` you can use a template to render a block of multiple elements.


`:key` attribute:  
- Should be a primitive type
- Recommended unless you want to intentionally rely on the default behavior
- Required when using `v-for` with a custom component
<!-- :key => React=key, Angular=trackBy -->



[Events guide](https://vuejs.org/guide/essentials/event-handling.html#event-handling)
{: .title-url}
## Events
```html
<button v-on:click="nr++"></button>
<form @submit="methodName"></form>
<input @keyup.enter="fn(arg1, arg2, ...)">
```

#### Event Modifiers
- `@keyup.enter`: `.enter` is called a [Key Modifier](https://vuejs.org/guide/essentials/event-handling.html#key-modifiers)
- Modifiers are stackable: `.mod1.mod2`
- `@keyup.page-down="onPageDown"`
- `.prevent` == `event.preventDefault()`
- `.stop` == `event.stopPropagation()`: Shorthand `~`.
- `.once`: Unregister after first trigger
- `.self`: Trigger only when the element is the `.target`
- `@click.ctrl.exact`: Only trigger when ctrl is pressed without any other keys
- `@click.capture`: An event on an inner element is handled here before the `.target`. Shorthand `!`.
- [`.passive`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters): Corresponds to `addEventListener`'s passive option. Shorthand `&`.


<!-- TODO: not covered? .native modifier etc -->
<!-- .native (native events in custom components) -->
<!-- v-on:update:propName="doStuff" ==> .sync == two way binding? -->





# Components

[Components guide](https://vuejs.org/guide/essentials/component-basics.html#components-basics)
{: .title-url}

#### Usage
```javascript
// Html
// <product :premium="premium" @add-to-cart="updateCart" class="main-prod"></product>

// JavaScript
new Vue({
  data: {
    premium: true,
    cart: [],
  },
  methods: {
    updateCart(product) {
      this.cart.push(product);
    }
  }
});
```

#### Declare

```javascript
{% raw %}const comp = Vue.component('product', {
  // Props with validation
  props: {
    premium: {type: Boolean, required: true, default: false},
    // Types: String, Number, Array, Object, Function, Promise, any ctor
    // Null and undefined always pass validation
    // oneOf: [String, Number],
    // special: {
    //   default() { return 'calculatedValue'; },
    //   validator(value) { return true; }
    // }
  },
  // Props without validation
  // props: ['premium'],
  template: `
    <!-- Must have single root element -->
    <div class="product">
      <h1>{{ product }}</h1>
      <p v-if="premium"><b>FREE Shipping</b></p>

      <button @click="addToCart">
        Add to Cart
      </button>
    </div>
  `,
  data() {
    // data is a function inside a Vue.component()
    // Do return a new object reference each time.
    return {
      product: 'Socks',
      inventory: 3,
    };
  },
  methods: {
    addToCart() {
      this.inventory--;
      this.$emit('add-to-cart', {product: this.product});
    }
  },

  // Local Subcomponent Registration
  // const LocalComponent = {};
  // components: {
  //   'local-component': LocalComponent,
  // }

  // Watches
  watch: {
    iventory(newInventory, oldInventory) {
      // TODO: Call backend to check if product is still available
    }
  },


  // LifeCycle Methods
  created() {
    console.log('Http requests probably here');
  },
  mounted() {},
  updated() {},
  destroyed() {},
  beforeDestroy() {},
});{% endraw %}
```

## Slots

Slots, like React `props.children` or Angular `ng-content`.

[Slots guide](https://vuejs.org/guide/components/slots.html#slots)
{: .title-url}

#### Usage
```html
<alert-box>
  <!-- Named Slot -->
  <template v-slot:title>
    <!-- Shorthand #title -->
    Oh noes!
  </template>

  <!-- Default Slot -->
  Something bad happened.
  <!-- Could put default slot into: v-slot:default -->
</alert-box>
```

#### Declare
```javascript
Vue.component('alert-box', {
  template: `
    <div>
      <slot name="title">Error!</slot>
      <slot>Default value</slot>
    </div>
  `
});
```



# Forms

[Forms guide](https://vuejs.org/guide/essentials/forms.html#form-input-bindings)
{: .title-url}

#### Html
```html
{% raw %}<form @submit.prevent="onSubmit">
  <input v-model="name" @keydown.ctrl.v.prevent="blockPaste">

  <div v-for="error in errors" :key="error">
    * {{ error }}
  </div>

  <select v-model.number="rating">
    <!-- Other modifiers: -->
    <!-- .lazy => Sync on change event -->
    <!-- .trim => Strip whitespace -->
    <option disabled value="">select</option>
    <option v-for="i in 5" :key="i">{{ i }}</option>
  </select>

  <br>
  <input
    type="checkbox"
    v-model="acceptTerms"
    true-value="yes"
    false-value="no"
  >
  Accept terms

  <button>Submit</button>
</form>{% endraw %}
```

#### JavaScript
```javascript
new Vue({
  data: {
    name: null,
    rating: '',
    acceptTerms: false,
    errors: []
  },
  methods: {
    onSubmit() {
      this.errors = [];
      if (!this.rating) {
        this.errors.push('Please select a rating');
        return;
      }

      const review = {name: this.name, rating: this.rating};
      console.log('Submitting', review);
    },
    blockPaste() {
      console.log('Control + V is not allowed!');
    }
  },
});
```

#### Validation not included

{% include github-stars.html url="vuelidate/vuelidate" desc="Simple, lightweight model-based validation for Vue.js" %}
{% include github-stars.html url="baianat/vee-validate" desc="Template Based Validation Framework for Vue.js" %}


#### Caveat

Reuse of `<input>` elements could lead to strange behavior.

```html
<template v-if="loginType === 'username'">
  <!-- Using `key` so the inputs are not reused when loginType changes -->
  <input placeholder="Enter your username" key="username-input">
</template>

<template v-else>
  <input placeholder="Enter your email address" key="email-input">
</template>
```


[Using v-model on Components](https://vuejs.org/guide/components/v-model.html#component-v-model)
{: .title-url}

## Custom Model Binding

```javascript
Vue.component('custom-input', {
    props: ['value'],
    template: `<input :value="value" @input="$emit('input', $event.target.value)">`
})

// Html
// <custom-input v-model="searchText"></custom-input>

new Vue({
    el: '#app',
    data: {
      searchText: ''
    },
});
```



# Reactivity

#### Array

Vue.js has wrapped array functions so that they are reactive:  
`push`, `pop`, `shift`, `unshift`, `splice`, `sort`, `reverse`

Assigning a new array will reuse DOM elements if possible on the rerender.

It **will not detect `this.items[0] = 'newVal'`**. If you must, use:  
```javascript
Vue.set(vm.items, indexOfItem, newValue);
vm.items.splice(indexOfItem, 1, newValue);
vm.$set(vm.items, indexOfItem, newValue);
```


#### Objects

Assigning a new property to an object is not reactive.
Assign it a default value on initialization.

If you must, use:  
```javascript
Vue.set(vm.userProfile, 'age', 27);
vm.$set(vm.userProfile, 'age', 27);
```

<br>
# Closing Thoughts

I was really surprised by Vue's simplicity and terse syntax due its many shorthands.
Tooling in Visual Studio Code and Chrome is really good. The CLI was pretty amazing
at setting up a full blown starter project.

So, I learned me some Vue.js for a technical session
and am already set on using it for a few small projects.
