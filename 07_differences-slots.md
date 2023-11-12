Slots
=====

This lesson is about how to use _children prop_ and _render props_ in a Vue.js component. But in here, they go by different names, _“default slot”_ for _“children prop”_, and _“named slots”_ for _“render props”_.

Next, we’ll talk about how to use _default slot_ and _named slots_ to refactor the code.

* * *

Default Slot
------------

Let’s start with _default slot_ because it’s simpler.

Like I said, default slot is basically _the Vue.js equivalent of the children prop_.

For example, if we don’t want the `reviews` data to be passed to the new component, we can just pass the `<ul>` markup as the child element of `<ReviewList>`:

📃 **src/components/ProductDisplay.vue**

    <ReviewList>
      <ul>
        <li v-for="(review, index) in reviews" :key="index">
          <span>{{ review.name }} gave this {{ review.rating }} stars</span>
          <br/>
          <span>{{ review.content }}</span>
        </li>
      </ul>
    </ReviewList>
    

Then inside **ReviewList.vue**, we don’t need the `defineProps`, but we have to use the `<slot>` element to render the markup passed from the parent:

📃 **src/components/ReviewList.vue**

    <script setup>
    // defineProps({
    //   reviews: {
    //     type: Array,
    //     required: true
    //   }
    // })
    </script>
    
    <template>
      <div class="review-container">
        <h3>Reviews:</h3>
        <slot></slot>
      </div>
    </template>
    

If you had experience using React’s _children prop_, this should feel very familiar.

* * *

Named Slots
-----------

Now, let’s talk about _named slots_, which are basically _the Vue.js equivalent of render props_.

Let’s say, we changed our mind, and we want the `reviews` data to be passed as a prop to `ReviewList` just like before:

📃 **src/components/ReviewList.vue**

    <script setup>
    // uncomment this
    defineProps({
      reviews: {
        type: Array,
        required: true
      }
    })
    </script>
    

But, we want the heading and the item markup to be passed from the parent:

📃 **src/components/ReviewList.vue**

    <template>
      <div class="review-container">
        <h3>
          <!-- heading from the parent -->
        </h3>
        <ul>
          <li v-for="(review, index) in reviews" :key="index">
            <!-- item markup from the parent -->
          </li>
        </ul>
      </div>
    </template>
    

We can just use `<slot>` again, but this time with a `name`:

📃 **src/components/ReviewList.vue**

    <template>
      <div class="review-container">
        <h3>
          <slot name="heading"></slot>
        </h3>
        <ul>
          <li v-for="(review, index) in reviews" :key="index">
            <slot name="item"></slot>
          </li>
        </ul>
      </div>
    </template>
    

Now in **ProductDisplay**, we have to pass two named `<template>` elements to fill in the slots:

📃 **src/components/ProductDisplay.vue**

    <ReviewList :reviews="reviews">
      <template #heading>
        Reviews:
      </template>
      <template #item>
        <span>{{ review.name }} gave this {{ review.rating }} stars</span>
        <br/>
        <span>{{ review.content }}</span>
      </template>
    </ReviewList>
    

They’re similar to default slot, but with names.

* * *

Default slot can also be written in this same syntax with `<template>`, so the two versions here are technically the same:

**Simple version:**

    <ReviewList>
      <ul>
        <li v-for="(review, index) in reviews" :key="index">
          <span>{{ review.name }} gave this {{ review.rating }} stars</span>
          <br/>
          <span>{{ review.content }}</span>
        </li>
      </ul>
    </ReviewList>
    
    

**Template version:**

    <ReviewList>
      <template #default>
        <ul>
          <li v-for="(review, index) in reviews" :key="index">
            <span>{{ review.name }} gave this {{ review.rating }} stars</span>
            <br/>
            <span>{{ review.content }}</span>
          </li>
        </ul>
      </template>
    </ReviewList>
    

* * *

Slot Props
----------

We’re passing two named templates.

The `<template #heading>` should be working just fine.

But, the `<template #item>` still needs access to the variable `review`, which is not currently available in the **ProductDisplay** component:

📃 **src/components/ProductDisplay.vue**

    <ReviewList :reviews="reviews">
      <template #heading>
        Reviews:
      </template>
      <template #item>
        <span>{{ review.name }} gave this {{ review.rating }} stars</span>
        <br/>
        <span>{{ review.content }}</span>
      </template>
    </ReviewList>
    

The `review` variable is living inside **ReviewList:**

📃 **src/components/ReviewList.vue**

    <li v-for="(review, index) in reviews" :key="index">
      <slot name="item"></slot>
    </li>
    

Just like using _render props_ in React, we have to pass the `review` variable from the child component back to the parent component so that the `<template #item>` in the parent component can access the `review` variable.

We can pass the `review` variable using a prop binding:

📃 **src/components/ReviewList.vue**

    <li v-for="(review, index) in reviews" :key="index">
      <slot name="item" :review="review"></slot>
    </li>
    

Now, in **ProductDisplay**, we will be able to access the `review` data using the `slotProps`:

📃 **src/components/ProductDisplay.vue**

    <template #item="slotProps">
      <span>{{ slotProps.review.name }} gave this {{ slotProps.review.rating }} stars</span>
      <br/>
      <span>{{ slotProps.review.content }}</span>
    </template>
    

If you think of each named `<template>` passed to **ReviewList** as a _render prop function_, the `slotProps` would be like the _parameter of the render prop function._

So, you can name it anything you like, it doesn’t have to be _slotProps_.

We can even _not_ name it, and just use the destructuring syntax:

📃 **src/components/ProductDisplay.vue**

    <template #item="{ review }">
      <span>{{ review.name }} gave this {{ review.rating }} stars</span>
      <br/>
      <span>{{ review.content }}</span>
    </template>
    

As you can see, a _named slot_ is visually different to a _render prop_, but they’re functionally similar.

* * *

Just like event subscriptions, the content you pass down for a slot is optional. So if you remove the named templates, there won’t be any error.

But `<slot>` allows you to provide a fallback template by putting it inside the `<slot>` element:

📃 **src/components/ReviewList.vue**

    <li v-for="(review, index) in reviews" :key="index">
      <slot name="item" :review="review">
        <span>{{ review.name }}</span>: <span>{{ review.content }}</span>
      </slot>
    </li>
    

So if you didn’t pass any `<template>` from the parent, this fallback version would be used.

* * *

The End of the Props Trilogy
----------------------------

We’re finally done with _the Props Trilogy_. We have learned how to make components work together using

1.  _props_
2.  _custom events (the equivalent of React’s callback props),_
3.  _and slots (the equivalent of React’s children prop and render props)_

In the next lesson, we’ll talk about the various lifecycle hooks that can be used in a Vue.js component.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L7-start)
    
*   [Ending Code](https://github.com/Code-Pop/vue-for-react-devs/tree/Part-2-L7-end)
