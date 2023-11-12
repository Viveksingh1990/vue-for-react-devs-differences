Custom Events
=============

In the previous lesson, we used props to send data from the **App** component to the **ProductDisplay** component. In this lesson, we’re going to send data in the _opposite_ direction using a custom event. Custom event is the Vue.js equivalent of callback prop.

* * *

* * *

Creating a custom event
-----------------------

The _Add to Cart_ button is currently not working because the button is now located in the **ProductDisplay** component while the **App** component is still in control of updating the `cart` ref.

We have to allow **ProductDisplay** to inform the **App** component whenever the button is clicked.

We’re going to fix this by defining a custom event in the **ProductDisplay** component:

📃 **src/components/ProductDisplay.vue**

    const emit = defineEmits(['add-to-cart'])
    

Just like `defineProps`, `defineEmits` is another compiler macro. You can put multiple custom event names in the array, but the only custom event we need `add-to-cart`.

`defineEmits` returns an `emit` function, and we can use this to trigger the `add-to-cart` event inside the `addToCart` function.

📃 **src/components/ProductDisplay.vue**

    const addToCart = () => {
      // cart.value += 1
      emit('add-to-cart')
    }
    

Since the `cart` ref is not living in **ProductDisplay**, we’re not going to increment the `cart` ref in `addToCart`. Instead, we’re emitting a custom event so that the **App** component can increment it.

Now, we have to go back to **App.vue** to listen to the event:

📃 **src/App.vue**

    <ProductDisplay ... @add-to-cart="updateCart"></ProductDisplay>
    

We’re listening to the `add-to-cart` custom event just the way we would for built-in events like `click` and `mouseover`.

Finally, create the `updateCart` function, and increment `cart`:

📃 **src/App.vue**

    const updateCart = () => {
      cart.value += 1
    }
    

Now, the _Add to Cart_ button should be functional again:

![button.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1669668490981.jpg?alt=media&token=23d0c6fa-e2c4-4121-b000-7108590cb8df)

* * *

Event with Payload
------------------

In practice, we usually have to pass some payload data when triggering an event. So let’s pass the id of the product as the second argument of `emit`:

📃 **src/components/ProductDisplay.vue**

    const addToCart = () => {
      emit('add-to-cart', variants.value[selectedVariant.value].id)
    }
    

We’re going copy this, and change this to id

Now, we will be able to receive this payload data in **App.vue** as the first parameter of the callback function:

📃 **src/App.vue**

    const updateCart = (id) => {
      cart.value += 1
    }
    

Let’s change the `cart` ref to an `array` so that we can push the `id` into the `cart`, instead of incrementing it:

📃 **src/App.vue**

    const cart = ref([])
    ...
    const updateCart = (id) => {
      cart.value.push(id)
    }
    

Now that `cart` is an array, we have to update the `<template>` to render `cart.length` instead of just `cart`:

📃 **src/App.vue**

    <div class="cart">Cart({{ cart.length }})</div>
    

Make sure that it’s still working in the browser:

![cart.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F2.1669668490982.jpg?alt=media&token=baeef212-72ed-40f2-8399-9013de09ca0f)

* * *

Custom Event vs Callback Prop
-----------------------------

Functionally, Vue’s custom event is similar to a callback prop in React. But custom events are built on an extra layer of _event-driven abstraction_.

With this abstraction, it’s fine for a component to emit an event even when there isn’t a callback function subscribed to the event. There won’t be any error.

**In Vue:**

    const addToCart = () => {
      emit('add-to-cart') // no error
    }
    

Therefore, subscriptions to an event are _effectively_ optional.

But in React, it’s different. When you trigger a callback prop, you’re basically just calling a function. So if that callback prop is meant to be optional, and it wasn’t passed to the component, it would be `undefined`, and calling an undefined prop would produce an error.

**In React:**

    export default function ProductDisplay(props) {
      ...
      const addToCart = () => {
        props.addToCart() // ERROR
      }
      ...
    

To fix this, you would have to wrap the call site in a conditional:

    export default function ProductDisplay(props) {
      ...
      const addToCart = () => {
        if(props.addToCart !== undefined) {
          props.addToCart() // no error
        }
      }
      ...
    

But with Vue’s _event-driven approach_, you don’t have to worry about undefined callback props at all.

* * *

Up Next
-------

Now that we have the knowledge to use props and custom events, we’ll combine these two concepts and learn about _two-way data binding_ in the next lesson.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L4-start)
    
*   [Ending Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L4-end)
