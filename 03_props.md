Props
=====

So far we’ve only had one component for the entire app. In this lesson, we’ll extract some code to a second component called **ProductDisplay**. And we’ll talk about how to set up props so that we can pass data from the **App** component to the **ProductDisplay** component.

The ProductDisplay Component
----------------------------

Other than the nav bar and the cart, we want to move everything to a new component.

First, create a **components** folder, and create a **ProductDisplay.vue** file in that folder.

Now, we can copy over all the `<script>` code, except the `cart` ref:

📃 **src/components/ProductDisplay.vue**

    <script setup>
    import { ref, computed } from 'vue'
    import socksGreenImage from './assets/images/socks_green.jpeg'
    import socksBlueImage from './assets/images/socks_blue.jpeg'
    
    const product = ref('Socks')
    const brand = ref('Vue Mastery')
    
    const selectedVariant = ref(0)
      
    const details = ref(['50% cotton', '30% wool', '20% polyester'])
    
    const variants = ref([
      { id: 2234, color: 'green', image: socksGreenImage, quantity: 50 },
      { id: 2235, color: 'blue', image: socksBlueImage, quantity: 0 },
    ])
    
    const title = computed(() => {
      return brand.value + ' ' + product.value
    })
    
    const image = computed(() => {
      return variants.value[selectedVariant.value].image
    })
    
    const inStock = computed(() => {
      return variants.value[selectedVariant.value].quantity > 0
    })
    
    const addToCart = () => cart.value += 1
    
    const updateVariant = (index) => {
      selectedVariant.value = index
    }
    </script>
    

Now for the `<template>` part, copy over the entire `<div class="product-display">` element to the new component:

📃 **src/components/ProductDisplay.vue**

    <template>
      <div class="product-display">
        <div class="product-container">
          <div class="product-image">    
            <img v-bind:src="image">
          </div>
          <div class="product-info">
            <h1>{{ title }}</h1>
            <p v-if="inStock">In Stock</p>
            <p v-else>Out of Stock</p>
            <p>Shipping: {{ shipping }}</p>
            <ul>
              <li v-for="detail in details">{{ detail }}</li>
            </ul>
            <div 
              v-for="(variant, index) in variants" 
              key="variant.key"
              @mouseover="updateVariant(index)"
              class="color-circle"
              :style="{ backgroundColor: variant.color }"
            >
            </div>
            <button
              class="button" 
              :class="{ disabledButton: !inStock }"
              :disabled="!inStock"
              v-on:click="addToCart"
            >
              Add to cart
            </button>
          </div>
        </div>
      </div>
    </template>
    

This new component is not functional yet. We still have to change the relative paths of the images because the **ProductDisplay.vue** file is located in a different folder.

We can add an extra dot to each relative path:

📃 **src/components/ProductDisplay.vue**

    import socksGreenImage from '../assets/images/socks_green.jpeg'
    import socksBlueImage from '../assets/images/socks_blue.jpeg'
    

Alternatively, we can use the absolute path syntax:

📃 **src/components/ProductDisplay.vue**

    import socksGreenImage from '@/assets/images/socks_green.jpeg'
    import socksBlueImage from '@/assets/images/socks_blue.jpeg'
    

This would be a more long-term solution. Now, it doesn’t matter where you move this component file, these two image imports will always work.

Back in **App.vue,** we have to remove all the code that we just copied to the new component.

It should look like this now:

📃 **src/App.vue**

    <script setup>
    import { ref } from 'vue'
    
    const cart = ref(0)
    
    </script>
      
    <template>
      <div class="nav-bar"></div>
      <div class="cart">Cart({{ cart }})</div>
    </template>
    

(Since we’re still using `ref` in the **App** component, we still need the `ref` import.)

Now to use the new **ProductDisplay** component, we just have to import it and use it as an HTML element in the template:

📃 **src/App.vue**

    <script setup>
    import { ref } from 'vue'
    import ProductDisplay from '@/components/ProductDisplay.vue'
    ...
    </script>
      
    <template>
      <div class="nav-bar"></div>
      <div class="cart">Cart({{ cart }})</div>
      <ProductDisplay></ProductDisplay>
    </template>
    

(Just like importing the images, we can also use absolute path for the component import.)

Check it in the browser to make sure that both components are showing up:

![browser_productDisplay.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1669666463926.jpg?alt=media&token=141948d0-03d8-4be9-92b9-1d69e7cf0034)

(You might have noticed that the cart is not updating when you click the button. We’ll fix that with a custom event in the next lesson.)

* * *

Define Props
------------

Now that we have two component, let’s talk about props.

We want to pass some data from the **App** component to the **ProductDisplay** component.

First, we have to define the prop that we want to receive in **ProductDisplay.**

We’ll use `defineProps`, which is a compiler macro. That means it’s not a runtime function so we don’t have to import it.

📃 **src/components/ProductDisplay.vue**

    import { ref, computed } from 'vue'
    import socksGreenImage from '@/assets/images/socks_green.jpeg'
    import socksBlueImage from '@/assets/images/socks_blue.jpeg'
    
    const props = defineProps({ 
      /* specifications of the props */ 
    })
    
    ...
    

It takes an object literal where we can specify the props we need.

For example, we want a prop called `premium`. It’s a `Boolean`, and it’s a `required` prop:

📃 **src/components/ProductDisplay.vue**

    const props = defineProps({
      premium: {
        type: Boolean,
        required: true
      }
    })
    

> `Boolean` is a native constructor of JavaScript itself, it’s not a Vue-specific object.

> _Required_ means that if the prop isn’t passed to the component, you will see a warning in the browser console.

* * *

Alternatively, you can just specify only the type with the shorthand syntax, in which case the prop will be optional:

    const props = defineProps({
      foo: String,
      ...
    })
    

* * *

If you want to allow a prop to be any one of multiple types, you can use an array to specify the types:

    const props = defineProps({
      foo: [String, Number],
      ...
    })
    

Now, this can be a string as well as a number.

But if you use the expanded syntax, you can set up a default value for the prop:

    const props = defineProps({
      foo: {
        type: [String, Number],
        default: 1
      },
      ...
    })
    

If the type is a reference type, such as an array or an object, you would have to use a factory function to set up the `default` value:

    const props = defineProps({
      bar: {
        type: Array,
        default: () => { return [1,2,3] }
      },
      ...
    })
    

* * *

Prop Binding
------------

Now that we have a `premium` prop required for the **ProductDisplay** component. We have to create the premium ref in **App.vue**, and pass that to the ProductDisplay element via a _binding_:

📃 **src/App.vue**

    const premium = ref(true)
    ...  
    <template>
      ...
      <ProductDisplay :premium="premium"></ProductDisplay>
    </template>
    

* * *

Now go back to **ProductDisplay.**

To make use of the prop, let’s create a new computed property `shipping`. This is the shipping fee that will be calculated based on the `premium` prop. If our user is premium, their shipping is _free_, otherwise, it’s _2.99_.

📃 **src/components/ProductDisplay.vue**

    const shipping = computed(() => {
      if (props.premium) {
        return 'Free'
      }
      else {
        return 2.99
      }
    })
    

![browser_free.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F2.1669666463927.jpg?alt=media&token=c29bc803-e54e-417e-86d2-7b206b4bf4e7)

* * *

Finally, render the shipping computed property in the template:

    <p v-if="inStock">In Stock</p>
    <p v-else>Out of Stock</p>
    <p>Shipping: {{ shipping }}</p>
    
    

Notice that we don’t need `.value` to access the value of a prop like we did with refs. This is a sign that we should not be changing the value of a prop.

📃 **src/components/ProductDisplay.vue**

    if (props.premium) {
      ...
    

* * *

What about callback props?
--------------------------

Technically, you can set the type of a prop to be `Function` and pass a callback function as a prop just like in React. But the Vue.js way to set up callback props is by defining/emitting a custom event. We’ll talk about that in the next lesson.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L3-start)
    
*   [Ending Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L3-end)
