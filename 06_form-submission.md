Form Submission
===============

Picking up from the previous lesson, we have to finish the form component by listening to the form `submit` event and processing the form data.

Submitting the form
-------------------

Just like in a React app, we want to prevent the default submission and replace that with our own callback function.

We can do that by listening to the `@submit` event with the `prevent` modifier:

📃 **src/components/ReviewForm.vue**

    <form class="review-form" @submit.prevent="onSubmit">
      ...
    </form>
    

Finally, create the `onSubmit` callback and emit a new event with the form data:

📃 **src/components/ReviewForm.vue**

    const emit = defineEmits(['review-submitted'])
    ...
    const onSubmit = () => {
      const reviewData = JSON.parse(JSON.stringify(review))
      emit('review-submitted', reviewData)
    }
    

The reason why we’re sending a _stringified_ object of the form data instead of the original reactive object is because we want to reuse this variable for future form submissions. And so we don’t want the data that we’re sending out of this form to still be reactive.

* * *

Clearing the form
-----------------

After the `emit`, we have to clear out all the form fields:

    const onSubmit = () => {
      const reviewData = JSON.parse(JSON.stringify(review))
      emit('review-submitted', reviewData)
    
      // reset
      review.name = ''
      review.content = ''
      review.rating = null
    }
    

* * *

If you don’t like having multiple statements to clear our all the form fields, you can use `Object.assign`:

    Object.assign(review, {
      name: '',
      content: '',
      rating: null
    })
    

* * *

If you don’t like repeating the same form data in multiple places, you can extract it to a variable that you can reuse for both _initialization_ and _reset_:

    const defaultFormData = {
      name: '',
      content: '',
      rating: null
    }
    
    // initialization
    const review = reactive({...defaultFormData})
    ...
    const onSubmit = () => {
      ...
      // reset
      Object.assign(review, defaultFormData)
    }
    

But make sure that you’re using the three-dot syntax to create a new object with `defaultFormData` for `reactive()`. If you pass `defaultFormData` directly into `reactive()`, it will get modified because the review object will be linked to the `defaultFormData` object.

So if you want a more fool-proof method, you can wrap the data in a function:

    const getDefaultFormData = () => ({
      name: '',
      content: '',
      rating: null
    })
    
    // initialization
    const review = reactive(getDefaultFormData())
    ...
    const onSubmit = () => {
      ...
      // reset
      Object.assign(review, getDefaultFormData())
    }
    

* * *

Handling the custom event
-------------------------

Now that the form is ready, we can go back to **ProductDisplay** and import the form component:

    <script setup>
    import { ref, computed } from 'vue'
    import ReviewForm from '@/components/ReviewForm.vue'
    

Put the `ReviewForm` element at the bottom of `product-display`, and listen for the `review-submitted` event:

    <template>  
      <div class="product-display">  
        ...
        <ReviewForm @review-submitted="addReview"></ReviewForm>
      </div>
    </template>
    

Create an array ref called `reviews`, and set up the `addReview` callback to add the review data to this new ref:

    const reviews = ref([])
    ...
    const addReview = (review) => {
      reviews.value.push(review)
    }
    

To make sure that this is working, you can `console.log` the reviews value:

    const addReview = (review) => {
      reviews.value.push(review)
      console.log(reviews.value)
    }
    

After submitting a review using the form, you should see a proxy object in the console, it has a `[[target]]` field pointing to an array containing the form data you just submitted:

![submit_console.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1669675156886.jpg?alt=media&token=34cbeef1-4795-4041-acbc-d461156ec3f1)

* * *

Showing the reviews
-------------------

Now we’re going to display the list of reviews. We have to first create a new component for that:

📃 **src/components/ReviewList.vue**

    <script setup>
    defineProps({
      reviews: {
        type: Array,
        required: true
      }
    })
    </script>
    
    <template>
      <div class="review-container">
        <h3>Reviews:</h3>
        <ul>
          <li v-for="(review, index) in reviews" :key="index">
            <span>{{ review.name }} gave this {{ review.rating }} stars</span>
            <br/>
            <span>{{ review.content }}</span>
          </li>
        </ul>
      </div>
    </template>
    

It’s a simple component that receives an array prop and render it using v-for.

Since we’re not using the prop in the script, we don’t have to create a variable for the props. The `review` prop will be made available to the `<template>` automatically.

Back in **ProductDisplay**, we have to import the component, and render `<ReviewList>` right above the `<ReviewForm>`:

📃 **src/components/ProductDisplay.vue**

    <script>
    import ReviewList from '@/components/ReviewList.vue'
    ...
    <ReviewList></ReviewList>
    <ReviewForm @review-submitted="addReview"></ReviewForm>
    
    

And then pass the `reviews` data as a prop:

📃 **src/components/ProductDisplay.vue**

    <ReviewList :reviews="reviews"></ReviewList>
    

If you fill out and submit the form, you should now see the new review appearing in the _Reviews_ container:

![review.png](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F2.1669675156887.jpg?alt=media&token=e1357dc2-4796-4ef3-b2f6-7f698eb5dbab)

* * *

Not Done Yet
------------

Now that we have more components in our app, we’re going to explore the different patterns of mixing the components with _slot_ in the next lesson.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L6-start)
    
*   [Ending Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L6-end)
