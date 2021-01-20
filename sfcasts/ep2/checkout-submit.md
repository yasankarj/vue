# Checkout Submit

To finish checkout, we need to submit this form via AJAX to an API endpoint. Copy
the URL and open up a new tab to go to `/api`: the home of our API documentation.

## POST /api/purchases

One API resource in here is called `Purchase`. *This* is what we will be using
for checkout: we'll send a POST request to `/api/purchases` to create a *new*
"purchase". The fields it needs are pretty familiar: the six customer fields that
we have in our form plus a `purchaseItems` array field. Each item in this array
will be an object with `product`, `color` and `quantity` keys: the *exact* structure
of the items in our `cart` data.

To help us talk to this endpoint, in `assets/services/`, create a new file called
`checkout-service.js`. Inside, I'll paste a function... which is only about one
line long. We import `axios`, export a function called `createOrder`, which takes
in that data structure we discussed, and posts to `/api/purchases`.

## Adding the "Order" Button

Cool! Let's go use this! In the checkout form component - `index.vue` - after the
last field, I'll paste in a submit button. Make sure to paste this *outside* of
the `form-col` div - I just pasted it *inside*... and I'll regret it in a few
minutes.

Anyways, there's nothing special about this: just `<button type="submit">`.

We're also going to need a loading animation while this is saving. So down in
`data`, add a new key: `loading: false`.

Then pull in our trusty loading component: import `Loading` from
`@/components/loading`. Add that to `components`:

... and up in the template, inside that new div, say `<loading/>` with
`v-show="loading"`.

## Sending the AJAX Call

Our next step is pretty straightforward: we need to add a method that, when the
form submits, sends the Ajax request. Down under `methods` add a new one called
`async onSubmit()`. I'm immediately making this `async` because I know I'm going
to want to `await` for the AJAX call to finish. Start with `this.loading = true`.

Because this is a checkout form, I don't want *anything* weird to happen. And so,
I'm going to write more error handling than we've done so far. Add a
`try {} catch` *and* a `finally`. Inside that last section, set
`this.loading = false`.

Thanks to this - whether we're successful or not - the loading animation will hide.

Inside `try`, say `const response =` `await createOrder()`. Make sure you hit tab
so that PhpStorm adds the import on top.

For the data argument, we need to pass the individual form fields and an extra
`purchaseItems` field. The form fields are stored on the `form` data. So
what we can do down here is say `...this.form`. That will expand the object so
that the data will have those 6 keys.

The last field we need to send is `purchaseItems`. For now, set that to an empty
array.

Down in catch, use `console.error()` - that's like `console.log()`... but will
look red - to log `error.response`. In Axios, if an AJAX call fails, it stores
the error response on this key. Oh, and also `console.log()` the success response
up in `try`.... actually `response.data`.

Awesome! We're eventually going to *do* something on success and error, but
this is a good start.

## Grabbing the Purchase Items

Let's fill in the empty `purchaseItems`. Go back to the Vue dev tools and click on
the `ShoppingCart` component. Not by accident... because our API is fairly consistent,
the `purchaseItems` key that we need to send matches the `cart.items` data exactly,
where each item has `color`, `product` and `quantity`.

This means that we need access to the `cart` object inside our checkout
form. Over in `index.vue`, we don't have access to that yet, so let's add a new
prop so it can be passed in. Add `props`, then `cart` with `type: Object` and
`required: true`.

Before we use that, open `shopping-cart.vue`. This is the component that *renders*
the `CheckoutForm` component: On `<checkout-form`, pass `cart="cart"`. I mean `:cart=cart`.

Back over in `index.vue`, now that the `cart` prop exists, use it: set
`purchaseItems` to `this.cart.items`.

## Hooking up the @submit

Ok! Now that this method is... kind of done, let's hook it up! On submit of the
form, we want Vue to call it. Up on the `<form>` tag... here it is - add
`@submit="onSubmit"`.

Testing time! Move over and click "check out". Uh... that button is *not* in the
right place. Come on Ryan!

Move the button div *outside* of the column. Now... much better.

## Form Prevent Default

Ok: submit the empty form. It... kinda looked like it worked? I saw the loading
animation... and then the page refreshed. Duh. I forgot to *prevent* the form from
its "default" behavior.

The easiest way to fix this is down inside the `onSubmit` method, this will receive
an `event` argument... and then we can say `event.preventDefault()`.

Easy peasy. *Or*... we can use the fancy Vue way! Remove all of that... then go
up to the form element where we call this. Change this to `@submit.prevent`.

Oooo. That `.prevent` is one of a small number of "event modifiers". This says,
"prevent the default". There are others like `.stop` and `.once` that are less
commonly used.

Let's try it again! Go to check out and... awesome! I saw the loading animation
for *just* a moment and then it went away. Go check the console. Woo! A 400
bad request and a `console.error()` of the response object.

Why did our AJAX call fail? Because our API already has built-in validation rules.
Next: let's handle when the AJAX call fails: both to render a message if there is
an *unexpected* error and, after, to render error messages next to each field that
fails validation.