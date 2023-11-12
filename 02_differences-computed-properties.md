Computed Properties
===================

In the previous lesson, we talked about the template being an implicit subscriber of the component’s state. But what about _explicit subscriber_?

An example of that would be a _computed property_. Let’s see how to apply that in our app.

* * *

Computed Property
-----------------

First, create a new state for the `brand` ref (below `product`):

📃 **src/App.vue**

    const product = ref('Socks')
    const brand = ref('Vue Mastery')
    

Let’s say we want to display both the brand name and the product name in the heading.

We can simply combine them together like this:

📃 **src/App.vue**

    <h1>{{ brand + ' ' + product }}</h1>
    

It will show up on the page like this:

![title.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1669665193485.jpg?alt=media&token=cf77a9f8-e23f-4afc-985c-f03a304f90f8)

But there’s a cleaner way to do this with a _computed property._

Let’s create one called `title`.

📃 **src/App.vue**

    import { ref, computed } from 'vue'
    ...
    const title = computed(() => {
      return brand.value + ' ' + product.value
    })
    ...
    

(Since we’re using the `computed` function, we have to import it from `vue`)

The `computed` function takes a callback as argument.

Inside the function, we’re returning a new string based off the values of our `brand` and `product` (”Vue Mastery Socks”, in this case). This will become the value of the computed property.

And from now on, whenever `brand` or `product` is changed, the value of `title` will get recalculated automatically.

This is different from React’s approach, where the entire component function would get executed every time something is changed. In Vue.js, only the callback of the computed property would get re-executed.

And finally in the template, we have to render `title`.

📃 **src/App.vue**

    <h1>{{ title }}</h1>
    

As you can see, accessing the value of `title` in the `<template>` doesn’t require using the `value` property. But if you’re accessing the value in the `<script>`, you would have to use `.value` just like you would with a `ref` object.

In the browser, make sure that it still shows “Vue Mastery Socks.”

* * *

Refactoring image
-----------------

With our new knowledge of computed property, let’s improve the variant selection process.

Currently, we’re displaying the image using the `image` ref:

📃 **src/App.vue**

    const image = ref(socksGreenImage)
    

And we’re updating the image using the `updateImage` callback:

📃 **src/App.vue**

    const updateImage = (variantImage) => {
      image.value = variantImage
    }
    

We can improve the code by turning the ref into a computed property, and it will be based on the `variants` data and a new `selectedVariant` ref.

📃 **src/App.vue**

    const selectedVariant = ref(0)
    
    const image = computed(() => {
      return variants.value[selectedVariant.value].image
    })
    

Once again, we’re using the variants data with a new ref, this `selectedVariant` ref is for keeping track of the index of the currently selected variant.

And now when we update the image in `updateImage`, we should be updating the new `selectedVariant` ref instead (and let’s also rename the function to `updateVariant`):

📃 **src/App.vue**

    const updateVariant = (index) => {
      selectedVariant.value = index
    }
    

Instead of changing the value of `image`, we are changing the value of `selectedVariant` here. And we’re setting it to the `index` that we pass into this function.

The new `index` parameter is a number that represents the choice of variant that the user chooses with the `mouseover` event. It doesn’t get provided to us automatically.

So we have to go back to where the `v-for` directive is used, and use the expanded syntax to get the `index` for each iteration:

📃 **src/App.vue**

    <div 
      v-for="(variant, index) in variants"
      ...
    

And with this `index` variable, we can pass it to the `updateVariant` function on `@mouseover`:

📃 **src/App.vue**

    <div 
      v-for="(variant, index) in variants" 
      ...
      @mouseover="updateVariant(index)"
    

* * *

So now when the `mouseover` event is triggered, the `updateVariant` function will be called with the `index`:

    const updateVariant = (index) => {
      selectedVariant.value = index
    }
    

And once the `selectedVariant` ref gets updated, the `image` computed property will be updated automatically:

    const image = computed(() => {
      return variants.value[selectedVariant.value].image
    })
    

* * *

In the browser, make sure that the variant selection still works:

![Screen Shot 2022-10-19 at 8.26.08 PM.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F2.1669665193486.jpg?alt=media&token=e94ce8d2-304c-4999-bbf5-b1bc1b2b1200)

This new way of updating the variant `image` is more scalable. Now we can easily make other information dynamic, such as the “In Stock” or “Out of Stock” status.

* * *

Refactor inStock
----------------

Currently, `inStock` is a ref with a static value `false`, and the mouseover event doesn’t affect it at all:

    const inStock = ref(false)
    

We can easily refactor `inStock` into a computed property just like `image`, and it will get updated based on the `selectedVariant` ref.

But first, we have to add a new `quantity` property to each variant:

📃 **src/App.vue**

    const variants = ref([
      { id: 2234, color: 'green', image: socksGreenImage, quantity: 50 },
      { id: 2235, color: 'blue', image: socksBlueImage, quantity: 0 },
    ])
    

We’ll use the `quantity` to determine the `inStock` status of each variant.

Now, we can remove `inStock` as a ref, and create it as another computed property, we just have to use `variants` with `selectedVariant` as the index along with the new `quantity` property:

📃 **src/App.vue**

    const inStock = computed(() => {
      return variants.value[selectedVariant.value].quantity > 0
    })
    

If the `quantity` is greater than zero, `inStock` will be `true`, otherwise it will be `false`.

* * *

Now both `image` and `inStock` are computed properties, and we don’t have to worry about how to update them. We just need to update the `selectedVariant` ref, and both of these computed properties will be updated automatically:

📃 **src/App.vue**

    const image = computed(() => {
      return variants.value[selectedVariant.value].image
    })
    
    const inStock = computed(() => {
      return variants.value[selectedVariant.value].quantity > 0
    })
    

* * *

In the browser, we are able to switch between two different variants, and both the _image_ and the _“in stock” status_ will be updated accordingly:

![browser.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F3.1669665199461.jpg?alt=media&token=d8df4841-2426-4b4e-bd01-71e61fd7ff0f)

> The `computed` function is part of the Vue 3 Composition API, so you can also use it inside a _composable_ (AKA _custom hook_).

* * *

Other functions
---------------

The Composition API also provides a few other useful functions for _reacting to state changes_, such as `watch` and `watchEffect`.

### **watch**

Sometimes, we want to be notified of a state change, but not necessarily to create a computed property based on it.

The `watch` function can help you to run a callback whenever a particular state is changed.

    import { ref, watch } from 'vue'
    
    const foo = ref(0)
    
    watch(foo, () => {
      // do something when foo is changed
    })
    

There’s no need to return anything from this callback function.

* * *

### **watchEffect**

`watchEffect` is similar to `watch`, but you don’t have to specify what to watch. Any _subscribable_ object that appears inside the callback will be subscribed to automatically. This is useful for subscribing to multiple states, and you don’t want to manually list out all of them.

    import { ref, watchEffect } from 'vue'
    
    const foo = ref(0)
    const bar = ref(0)
    
    watchEffect(() => {
      console.log(foo.value, bar.value)
    })
    

But different from `watch`, `watchEffect` will run the callback once from start before any state change. `watch` will only run the callback when the source is changed.

Like `computed`, `watch` and `watchEffect` can be used in your own custom composables.

* * *

Coming Up
---------

In the next two lessons, we’ll talk about how components communicate with each other via props and custom events.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L2-start)
    
*   [Ending Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L2-end)
