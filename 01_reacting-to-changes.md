Reacting to changes
===================

Welcome back to _Vue.js for React Devs_.

Building on top of the Part 1 course, we’re going to continue our Vue.js journey with this app:

![Vue.js for React developers app](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1669663651006.jpg?alt=media&token=fbac0753-a3fa-4493-b882-501b25b78860)

But this time we’ll discuss more about the differences between Vue.js and React.js.

As a high-level preview, these are the main topics we’ll go through:

*   Computed Property
*   Props
*   Form Input Binding
*   Lifecycle Hooks

Props will be explained in several different lessons because Vue.js has a very different way of handling _callback props_ and _render props_, and they are _Custom Events_ and _Slots_ respectively.

But first, let’s talk about the most fundamental difference between Vue and React. And that is the way they _react to state changes._

* * *

Different ways of Reacting
--------------------------

As you can see, Vue is using `ref` while React is using `useState`. At first, you might think these functions are basically the same thing but just with different names and syntax. And you’d be right; they’re practically the same thing.

But in terms of runtime execution, they are _very_ different.

As you know, the code inside a React component will get executed again and again as part of the reactive process.

    import { useState } from 'react'
    
    export default function () {
      const [count, setCount] = useState(0)
      console.log("this will get logged many times")
      return <p onClick={()=>setCount(count+1)}>{count}</p>
    }
    

* * *

But on the other hand, the Vue.js script block only runs once.

    <script setup>
    import { ref } from 'vue'
    
    const count = ref(0)
    console.log("this will get logged only once")
    
    </script>
    
    <template>
      <p @click="() => count++">{{ count }}</p>
    </template>
    

That’s because the Vue.js framework was implemented differently, notably with the observer pattern.

A Vue.js state is basically an object that can be _subscribed to_. And the subscriber will get notified whenever a state change occurs. For example, the template of the component is an implicit subscriber of the state in the same component. That’s why when a state is changed, the template will re-run itself.

And that’s why the `<script>` doesn’t need to be run again and again.

* * *

In the next lesson, we’ll see how this observer pattern applies to creating computed properties.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L1-start)
    
*   [Ending Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L1-end)
