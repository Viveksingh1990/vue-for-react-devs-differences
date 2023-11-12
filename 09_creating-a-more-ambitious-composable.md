Creating a more ambitious composable
====================================

We’re going to combine what we’ve learned about the Reactivity API and lifecycle hooks to create a more ambitious composable `useCart`.

It will be responsible for handling all the cart-related activities.

* * *

First, let’s move the `cart` ref, the `onMounted` hook, and `updateCart` into the new function called useCart, and this will be our _composable_ function:

📃 **src/App.vue**

    function useCart () {
    
      // moved here
      const cart = ref([])
    
      // moved here
      onMounted(() => {
        fetch('http://localhost:3000/cart')
          .then(resp => resp.json())
          .then(data => cart.value = data.content)
      })
    
      // moved here
      const updateCart = (id) => {
        cart.value.push(id)
      }
    
      return { cart, updateCart }
    }
    
    const { cart, updateCart } = useCart()
    const premium = ref(true)
    

And return `cart` and `updateCart` so that they can be accessed outside of the function.

At this point, we have a working composable using a lifecycle hook, but we’re going to refine it.

* * *

Computed
--------

Since we only need to show the count of the items in the cart, let’s use `computed` to create `cartCount` ref:

📃 **src/App.vue**

    import { ref, onMounted, computed } from 'vue'
    ...
    function useCart () {
      const cart = ref([])
      
      // NEW
      const cartCount = computed(() => cart.value.length)
    
      onMounted(() => {
        fetch('http://localhost:3000/cart')
          .then(resp => resp.json())
          .then(data => cart.value = data.content)
      })
    
      const updateCart = (id) => {
        cart.value.push(id)
      }
    
      return { cart, updateCart }
    }
    

This will be a computed property with the number of items in the cart.

Now, we have to return `cartCount` instead of `cart`:

📃 **src/App.vue**

    function useCart () {
      ...
      return { cartCount, updateCart }
    }
    
    const { cartCount, updateCart } = useCart()
    

We also have to update the template to use `cartCount`:

📃 **src/App.vue**

    <div class="cart">Cart({{ cartCount }})</div>
    

Now, we have a working composable using a lifecycle hook and a computed property,

* * *

Watch
-----

Finally, we’re going to add a new feature with `watch`. This will update the server whenever the cart is updated:

📃 **src/App.vue**

    function useCart () {
      const cart = ref([])
    
      const cartCount = computed(() => cart.value.length)
    
      onMounted(() => {
        fetch('http://localhost:3000/cart')
          .then(resp => resp.json())
          .then(data => cart.value = data.content)
      })
    
      // NEW
      watch(cart, () => {
        fetch('http://localhost:3000/cart', {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            content: cart.value
          })
        })
      })
    
      const updateCart = (id) => {
        cart.value.push(id)
      }
    
      return { cartCount, updateCart }
    }
    

But this is not going to work as is. Since `watch` will only be watching reassignment updates of the target `ref`, it’s not going to detect the change with `cart.value.push` in `updateCart`.

So first, we need to modify `updateCart` with an assignment:

📃 **src/App.vue**

    const updateCart = (id) => {
      // cart.value.push(id)
      cart.value = cart.value.concat(id)
    }
    

Now, `watch` will be able to watch every update on the `cart` ref and run the `fetch` logic to update the server data.

But in our case, we don’t really need to watch the first `cart` update since it’s just initialization based on server-side data, so it’s redundant to update the server with the same data that was originated from the server itself.

So we’re going to skip the server update on the first `cart` assignment.

First, set the initial `cart` value to `null`:

📃 **src/App.vue**

    function useCart () {
      const cart = ref(null)
      ...
    

And use a conditional to prevent the logic from running if the `oldValue` is `null`:

📃 **src/App.vue**

    watch(cart, (value, oldValue) => {
        // use a conditional
        if(oldValue === null) return
    
        fetch('http://localhost:3000/cart', {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            // use value in place of cart.value
            content: value
          })
        })
      })
    

And since we’re getting both `value` and `oldValue` from the callback parameters, we can just use `value` in place of `cart.value`.

We also have to modify the computed property to return `0` when `cart` is `null`:

📃 **src/App.vue**

    const cartCount = computed(() => {
      if (cart.value === null) {
        return 0
      }
      else {
        return cart.value.length
      }
    })
    

And we’re done with our `useCart` composable using a lifecycle hook, a computed property, and the `watch` function.

* * *

Here’s the full `useCart` function:

📃 **src/App.vue**

    function useCart () {
      const cart = ref(null)
    
      const cartCount = computed(() => {
        if (cart.value === null) {
          return 0
        }
        else {
          return cart.value.length
        }
      })
    
      onMounted(() => {
        fetch('http://localhost:3000/cart')
          .then(resp => resp.json())
          .then(data => cart.value = data.content)
      })
    
      watch(cart, (value, oldValue) => {
        if(oldValue === null) return
    
        fetch('http://localhost:3000/cart', {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            content: value
          })
        })
      })
    
      const updateCart = (id) => {
        cart.value = cart.value.concat(id)
      }
    
      return { cartCount, updateCart }
    }
    

Just like a custom hook in React, we can put this composable in a standalone file:

**src/composables/useCart.js**

    import { ref, onMounted, computed, watch } from 'vue'
    
    export default function useCart () {
      ...
    }
    

(copy over the import statement, and `export` the function)

Now back in the **App.vue** component, remove the imports that we no longer need in this file, and import the composable:

📃 **src/App.vue**

    import { ref } from 'vue'
    ...
    import useCart from '@/composables/useCart.js'
    
    const { cartCount, updateCart } = useCart()
    

And that’s it.

* * *

The Bottom Line
---------------

As you can see throughout this lesson, anything you can do in a custom React hook, you can do the same things in a Vue composable with the Composition API. But instead of relying on a single `useEffect` function to carry out most of the operations, Vue has separated specialized functions for different operations.

And that’s the API difference between Vue.js and React.js.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L9-start)
    
*   [Ending Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L9-end)
