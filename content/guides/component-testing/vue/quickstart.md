---
title: 'Vue Quickstart'
---

Welcome! This tutorial will walk you through creating a Vue app and using
Cypress Component Testing to test it. We assume you are already familiar with
Vue, but if this is your first time with Cypress, that's okay; we'll walk you
through all you need to know.

## Getting Started

### Create a Vue App

To start off, we are going to create a new Vue app.

We will use [Vite](https://vitejs.dev/) to create the app as it's quick to get
up and running. If you use any of the other
[supported frameworks](/guides/component-testing/vue/overview#Framework-Support)
in your project, the setup is the same.

To create a Vue project with Vite, run the following from your command prompt:

```bash
npm create vite@latest my-awesome-app -- --template vue
```

Go into the directory and run `npm install`:

```bash
cd my-awesome-app
npm install
```

> You can also download a Git repo with a fully working copy of this tutorial
> [here](https://github.com/cypress-io/component-testing-quickstart-apps/tree/main/vue).

### Install Cypress

Next, let's add Cypress to the app:

```bash
npm install cypress -D
```

Open Cypress:

```bash
npx cypress open
```

The Cypress Launchpad will guide you through configuring your project.

### Configuring Component Testing

Whenever you run Cypress for the first time, the app will prompt you to set up
either E2E Testing or Component Testing. Click on "Component Testing" to start
the configuration wizard.

<DocsImage 
  src="/img/guides/component-testing/select-test-type.jpg" 
  caption="Choose Component Testing"> </DocsImage>

The Project Setup screen automatically detects your framework and bundler,
which, in our case, is Vue and Vite. Click "Next Step" to continue.

<DocsImage 
  src="/img/guides/component-testing/project-setup-vue.jpg" 
  caption="Vue and Vite are automatically detected"> </DocsImage>

The next screen checks that all the required dependencies are installed. All the
items should have green checkboxes on them, indicating everything is good, so
click "Continue".

<DocsImage 
  src="/img/guides/component-testing/dependency-detection-vue.jpg" 
  caption="All necessary dependencies are installed"> </DocsImage>

Next, Cypress generates all the necessary configuration files and gives you a
list of all the changes it made to your project. Click "Continue".

<DocsImage 
  src="/img/guides/component-testing/scaffolded-files.jpg" 
  caption="The Cypress launchpad will scaffold all of these files for you">
</DocsImage>

After setting up component testing, you will be at the browser selection screen.

Pick the browser of your choice and click the "Start Component Testing" button
to open the Cypress test runner.

<DocsImage 
  src="/img/guides/component-testing/select-browser.jpg" 
  caption="Choose your browser"> </DocsImage>

When the test runner appears, it won't find any specs because we haven't created
any yet. However, we don't currently have a component, either. Let's change
that!

<DocsImage 
  src="/img/guides/component-testing/create-your-first-spec-vue.jpg">
</DocsImage>

### Creating a Component

At this point, your project is set up, and Cypress is ready to go, but we have
no components to test yet.

We will create a `<Stepper/>` component with zero dependencies and one bit of
internal state: a "counter" that can be incremented and decremented by the user.

Open the project with your favorite code editor and create a file at
**src/components/Stepper.vue**, then paste the following code into it:

<code-group>
<code-block label="src/components/Stepper.vue" active>

```html
<template>
  <div>
    <button data-cy="decrement" @click="decrement">-</button>
    <span data-cy="counter">{{ count }}</span>
    <button data-cy="increment" @click="increment">+</button>
  </div>
</template>

<script setup>
  import { ref } from 'vue'
  const props = defineProps(['initial'])

  const emit = defineEmits(['change'])

  const count = ref(props.initial || 0)

  const increment = () => {
    count.value++
    emit('change', count.value)
  }

  const decrement = () => {
    count.value--
    emit('change', count.value)
  }
</script>
```

</code-block>
</code-group>

Our component consists of two buttons, one used to decrement the counter and one
to increment it. A `span` tag sits in the middle of the buttons to show the
current value of the counter.

## Testing Vue Components

Now that you have a component, let's write a test to verify the component can
mount without any issues.

### Your First Component Test

To get started, go back to the Cypress test app and, in the "Create your first
spec" screen, click "Create from Vue component".

A modal will pop up listing all the components that is found in your app. Select
the **Stepper.vue** component:

<DocsImage 
  src="/img/guides/component-testing/create-from-component-vue.jpg">
</DocsImage>

A spec file was created at **src/component/Stepper.cy.js**:

<code-group>
<code-block label="src/components/Stepper.cy.js" active>

```js
import Stepper from './Stepper.vue'

describe('<Stepper />', () => {
  it('renders', () => {
    // see: https://test-utils.vuejs.org/guide/
    cy.mount(Stepper)
  })
})
```

</code-block>
</code-group>

> It is also possible to write Vue tests using JSX syntax. For more info, see
> [Using JSX](/guides/component-testing/vue/examples#Using-JSX).

Let's break down the spec. First, we import the `Stepper` component. Then, we
organize our tests using the functions `describe` and `it`, which we allow us to
group tests into sections by using method blocks. These are global functions
provided by Cypress, which means you don't have to import them directly to use
them. The top-level `describe` block will be the container for all our tests in
a file, and each `it` represents an individual test. The `describe` function
takes two parameters, the first of which is the name of the test suite, and the
second is a function that will execute the tests.

We defined a test using the `it` function inside `describe`. The first parameter
to `it` is a brief description of the spec, and the second parameter is a
function that contains the test code. In our example above, we only have one
test, but soon we'll see how we can add multiple `it` blocks inside of a
`describe` for a series of tests.

The test executes one command: `cy.mount(Stepper)`. The
[cy.mount()](/api/commands/mount) method will mount our component into the test
app so we can begin running tests against it.

Now it's time to see the test in action.

### Running the Test

Switch back to the browser you opened for testing, on click on the "Okay, run
the spec" button to execute it.

<DocsImage 
  src="/img/guides/component-testing/first-test-run-vue.jpg"> </DocsImage>

Our first test verifies the component can mount in it's default state without
any errors. If there is a runtime error during test execution, the test will
fail, and you will see a stack trace pointing to the source of the problem.

A basic test like the one above is an excellent way to start testing a
component. Cypress renders your component in a real browser, and you can use all
the techniques/tools you would normally during development, such as interacting
with the component in the test runner, and using the browser dev tools to
inspect and debug both your tests and the component's code.

Feel free to play around with the `Stepper` component by interacting with the
increment and decrement buttons.

Now that the component is mounted, our next step is to test that the behavior of
the component is correct.

### Selectors & Assertions

By default, the Stepper's counter is initialized to "0". We can override that
value by specifying an initial count. Let's write a couple of tests that will
verify both these states.

To do so, we will use a selector to access the `span` element that contains the
counter, and then assert that the text value of the element is what we expect it
to be.

There are various ways to select items from the DOM using Cypress. We will use
[cy.get()](/api/commands/get), which allows us to pass in a CSS-like selector.

After we "get" the element, we use the [should](/api/commands/should) assertion
method to verify it has the correct text value.

Add the following test inside the `describe` block, right below the first test:

<code-group>
<code-block label="src/components/Stepper.cy.js" active>

```js
it('stepper should default to 0', () => {
  cy.mount(Stepper)
  cy.get('span').should('have.text', '0')
})
```

</code-block>
</code-group>

When you go back to the test runner, you should see the test pass.

In the above test, we select the element by passing in "span" to `cy.get()`,
which will select all `span` tags in our component. We only have one `span`
currently, so this works. However, if our component evolves and we add another
`span`, then this test could start to fail. We should use a selector that will
be less brittle to future changes.

In the `Stepper` component, the `span` tag has a `data-cy` attribute on it:

```js
<span data-cy="counter">{{ count }}</span>
```

We assign a unique id to the `data-cy` attribute that we can use for testing
purposes. Update the test to use a CSS attribute selector to `cy.get()`:

<code-group>
<code-block label="src/components/Stepper.cy.js" active>

```js
it('stepper should default to 0', () => {
  cy.mount(Stepper)
  cy.get('[data-cy=counter]').should('have.text', '0')
})
```

</code-block>
</code-group>

The test will still pass as expected, and our selector is now future-proof. For
more info on writing good selectors, see our guide
[Selector Best Practices](/guides/references/best-practices#Selecting-Elements).

### Passing Props to Components

We should also have a test to ensure the `initial` prop sets the count to
something else besides its default value of "0". We can pass in props to the
`Stepper` component in the `mount` method:

<code-group>
<code-block label="src/components/Stepper.cy.js" active>

```js
it('supports an "initial" prop to set the value', () => {
  cy.mount(Stepper, { props: { initial: 100 } })
  cy.get('[data-cy=counter]').should('have.text', '100')
})
```

</code-block>
</code-group>

We pass in props using the `props` property in the options. See the
[MountOptions API](/guides/component-testing/vue/api#MountOptions) for more
info.

### Testing Interactions

We mounted and selected the element in the above tests but didn't interact with
it. We should also test that the value of the counter changes when a user clicks
the "increment" and "decrement" buttons.

To do so, we will interact with the component by using the
[click()](/api/commands/click) command, which clicks a DOM element just like a
real user would.

Add the following tests:

<code-group>
<code-block label="src/components/Stepper.cy.js" active>

```js
it('when the increment button is pressed, the counter is incremented', () => {
  cy.mount(Stepper)
  cy.get('[data-cy=increment]').click()
  cy.get('[data-cy=counter]').should('have.text', '1')
})

it('when the decrement button is pressed, the counter is decremented', () => {
  cy.mount(Stepper)
  cy.get('[data-cy=decrement]').click()
  cy.get('[data-cy=counter]').should('have.text', '-1')
})
```

</code-block>
</code-group>

When you view the results of each of the tests, you will see that the counter is
now "1" in the increment test, and "-1" in the decrement test. Not only did our
tests pass, but we can visually see the results in a browser!

<!-- Todo: add screenshot for above tests -->

View the
[Interacting with Elements](/guides/core-concepts/interacting-with-elements)
guide to learn more about other commands you can use to interact with the DOM.

## Testing Vue Components with Events

All the state of `<Stepper />` (ie: the count) is handled internally in the
component. Consumers are alerted to changes to the state by listening for a
`change` event.

As the developer of the Stepper component, you want to make sure when the
end-user clicks the increment and decrement buttons, that the `change` event is
emitted with the proper values.

### Using Spies

We can use [Cypress Spies](/guides/guides/stubs-spies-and-clocks#Spies) to
validate the `change` event is being emitted. A spy is a special function that
keeps track of how many times it was called and any parameters that it was
called with. We can pass a spy into the `change` event, interact with the
component, and then query the spy to validate it was called with the parameters
we expect.

Let's set up the spies and bind them to the component:

<code-group>
<code-block label="src/components/Stepper.cy.js" active>

```js
it('clicking + fires a change event with the incremented value', () => {
  const onChangeSpy = cy.spy().as('onChangeSpy')
  cy.mount(Stepper, { props: { onChange: onChangeSpy } })
  cy.get('[data-cy=increment]').click()
  cy.get('@onChangeSpy').should('have.been.calledWith', 1)
})
```

</code-block>
</code-group>

First, we create a new spy by calling the `cy.spy()` method. We pass in a string
that gives the spy an [alias](/guides/core-concepts/variables-and-aliases),
which assigns the spy a name by which we can reference it later. In
`cy.mount()`, we initialize the component and pass the spy into it. After that,
we click the increment button.

The next line is a bit different. We've seen how we can use the `cy.get()`
method to select elements, but we can also use it to grab any aliases we've set
up previously. We use `cy.get()` to grab the alias to the spy (by prepending an
ampersand to the alias name). We assert that the method was called with the
expected value.

With that, the `Stepper` component is well tested. Nice job!

## What's Next?

Congratulations, you covered the basics for component testing a Vue component
with Cypress!

To learn more about testing with Cypress, check out the
[Introduction to Cypress](/guides/core-concepts/introduction-to-cypress) guide.