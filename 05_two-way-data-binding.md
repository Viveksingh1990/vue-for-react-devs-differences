Two-Way Data Binding
====================

In this lesson, we’ll add a form on the product page to create comments. And we’ll see how to bind data to form inputs with the two-way data binding system in Vue.js.

Setting up the form component
-----------------------------

First, create a new component file with this template:

📃 **src/components/ReviewForm.vue**

    <template>
      <form class="review-form">
        <h3>Leave a review</h3>
        <label for="name">Name:</label>
        <input id="name">
    
        <label for="review">Review:</label>      
        <textarea id="review"></textarea>
    
        <label for="rating">Rating:</label>
        <select id="rating">
          <option>5</option>
          <option>4</option>
          <option>3</option>
          <option>2</option>
          <option>1</option>
        </select>
    
        <input class="button" type="submit" value="Submit">
      </form>
    </template>
    

We have three inputs here: a text input, a textarea, and a select. And a button for submitting the form.

And we’ll need some data for the input elements of the form:

📃 **src/components/ReviewForm.vue**

    <script setup>
    import { reactive } from 'vue'
    
    const review = reactive({
      name: '',
      content: '',
      rating: null
    })
    </script>
    
    <template>
      ...
    

This time we’re creating the state as a `reactive` object with three properties: name, content, and rating.

We’re not creating them as three separate refs because they are meant to be used together in a form.

And since we’re using `reactive`, we have to import it.

Next we have to bind the data and the input elements together.

Let’s go through our date-binding options.

* * *

The React Way
-------------

As you know, the React way of binding data to an input element is to create something called a controlled component.

    const [state, setState] = useState('')
    ...
    <input  
      id="name"
      value={state} 
      onInput={(event) => setState(evt.target.value)} 
    />
    

This controlled component has a value binding as well as an _onChange_ event binding. We also call this a two-way data binding.

We can do this in Vue.js, too:

    const state = ref('')
    ...
    <input 
      id="name"
      value="state" 
      @input="(event) => state = evt.target.value" 
    />
    

They’re pretty much identical aside from the minor syntactical differences.

But Vue.js being a framework, it has a much more concise way of handling two-way data binding.

* * *

The Vue Way
-----------

We can use the v-model directive to simplify and encapsulate the value binding and the event binding:

**Before**:

    const state = ref('')
    ...
    <input 
      type="text" 
      value="state" 
      @input="(event) => state = evt.target.value" 
    />
    

**After**:

    const state = ref('')
    ...
    <input
      type="text"
      v-model="state"
    />
    

These two versions are functionally the same. But as you can see, the `v-model` version is much cleaner.

Internally, `v-model` is smart enough to bind to the right attributes and the right events on different elements.

For example, if it’s a `textarea` element. You wouldn’t want to bind to its `value` attribute, which is an invalid attribute for `textarea`:

    <textarea value="state"></textarea>
    

Instead, you would bind to the text content of the element:

    <textarea>{{ state }}</textarea>
    

But if you’re using `v-model`, you don’t have to worry about what attribute and event to bind to:

    <textarea v-model="state"></textarea>
    

* * *

Create the Bindings
-------------------

Now that we know how to use `v-model`, let’s bind the three properties of the review object to the three input elements:

📃 **src/components/ReviewForm.vue**

    <input id="name" v-model="review.name">
    ...
    <textarea id="review" v-model="review.content"></textarea>
    ...
    <select id="rating" v-model="review.rating">
      <option>5</option>
      <option>4</option>
      <option>3</option>
      <option>2</option>
      <option>1</option>
    </select>
    

By default, all the form input data coming into the script will be string values. Since we want the rating field of our state to be a number, we have to convert that to a number by using a modifier: `v-model.number`

📃 **src/components/ReviewForm.vue**

    <select id="rating" v-model.number="review.rating">
    

* * *

Completing the Form
-------------------

Now all the input fields are ready, we just need to submit the form. We’ll see how to do that in the next lesson.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L5-start)
    
*   [Ending Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L5-end)
