Lifecycle Hooks
===============

This lesson is about using lifecycle hooks in Vue.js components.

Vue.js Lifecycle hooks are similar to lifecycle methods of a React class component as each hook has a user-friendly name that corresponds to a time in a component’s lifecycle.

`onMounted` is the Vue.js equivalent of `componentDidMount` `onUpdated` is the Vue.js equivalent of `componentDidUpdate` `onBeforeUnmount` is the Vue.js equivalent of `componentWillUnmount`

But these lifecycle hooks are _also_ similar to React hooks such as `useEffect` and `useState` as they can be used in creating your own custom hooks (or composables).

Therefore, the design of Vue.js lifecycle hooks is a mix of _user-friendliness_ and _API versatility_.

* * *

If you’ve used React hooks, you already know that a single `useEffect` function is used for lifecycle purposes.

If you want to do something after a component is mounted, you call `useEffect` with an empty array as the second argument:

    useEffect(() => {
      ...
    }, [])
    

If you want to do something after a component is updated, you call `useEffect` without a second argument:

    useEffect(() => {
      ...
    })
    

If you want to do something before a component is unmounted, you return a _cleanup_ function from within the `useEffect` callback:

    useEffect(() => {
      ...
      return () => {
        ...
      }
    })
    

To do these things in Vue.js, we just need to import and use the right lifecycle hook for each use case:

    import { onMounted, onUpdated, onBeforeUnmount } from 'vue'
    
    // after a component is mounted
    onMounted(() => {
      ...
    })
    
    //after a component is updated
    onUpdated(() => {
      ...
    })
    
    // before a component is unmounted
    onBeforeUnmount(() => {
      ...
    })
    

* * *

For instance, if we want to initialize the `cart` ref by loading data from an API server, we can set up the request inside the `onMounted` callback:

📃 **src/App.vue**

    import { ref, onMounted } from 'vue'
    
    const cart = ref([])
    ...
    
    onMounted(() => {
      fetch('[http://localhost:3000/cart](http://localhost:3000/cart)')
      .then(resp => resp.json())
      .then(data => cart.value = data.content)
    })
    

This is similar to how you would do it in React. Assuming that you have the this JSON data in a `db.json` file with `json-server` running:

📃 **db.json**

    {
      "cart": {
        "content": [2233]
      }
    }
    

* * *

In the browser, we should see the cart starts out with 1 item without us clicking the button:

![cart_1.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1669675941267.jpg?alt=media&token=1c14ffdf-1106-4512-9aef-9a6f8ee9944c)

* * *

Options API
-----------

This is our current **App.vue** component written in the **new** **Composition API**:

    <script setup>
    import { onMounted, ref } from 'vue'
    import ProductDisplay from '@/components/ProductDisplay.vue'
    
    const cart = ref([])
    const premium = ref(true)
    
    onMounted(() => {
      fetch('http://localhost:3000/cart')
        .then(resp => resp.json())
        .then(data => cart.value = data.content)
    })
    
    const updateCart = (id) => {
      cart.value.push(id)
    }
    </script>
    

This same component can also be written in the **traditional Options API**:

    <script>
    import ProductDisplay from '@/components/ProductDisplay.vue'
    
    export default({
      components: { ProductDisplay },
      data() {
        return {
          cart: [],
          premium: true
        }
      },
      mounted() {
        fetch('http://localhost:3000/cart')
          .then(resp => resp.json())
          .then(data => this.cart = data.content)
      },
      methods: {
        updateCart(id) {
          this.cart.push(id)
        }
      }
    })
    </script>
    

Aside from having to import `ProductDisplay` a second time using the `components` property, the two versions look structurally similar.

You can do pretty much the same thing with lifecycle hooks in the **traditional Options API**. But if you are using the **new Composition API**, you can use lifecycle hooks inside your composables, just like you can with `computed`, `watch`, and `watchEffect` that we’ve learned earlier in the course. And that’s a major benefit of using the **Composition API**.

* * *

In the next and final lesson, we’re going to combine `onMounted` with `computed`, `watch`, and `ref` to create a more ambitious composable.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L8-start)
    
*   [Ending Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L8-end)
