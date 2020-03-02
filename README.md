# angular-shepherd

<a href="https://shipshape.io/"><img src="http://i.imgur.com/DWHQjA5.png" alt="Ship Shape" width="100" height="100"/></a>

**[angular-shepherd is built and maintained by Ship Shape. Contact us for web app consulting, development, and training for your project](https://shipshape.io/)**.

[![npm version](https://badge.fury.io/js/angular-shepherd.svg)](http://badge.fury.io/js/angular-shepherd)
![Download count all time](https://img.shields.io/npm/dt/angular-shepherd.svg)
[![npm](https://img.shields.io/npm/dm/angular-shepherd.svg)]()
[![Build Status](https://travis-ci.org/shipshapecode/angular-shepherd.svg)](https://travis-ci.org/shipshapecode/angular-shepherd)
[![Code Climate](https://codeclimate.com/github/shipshapecode/angular-shepherd/badges/gpa.svg)](https://codeclimate.com/github/shipshapecode/angular-shepherd)
[![Test Coverage](https://codeclimate.com/github/shipshapecode/angular-shepherd/badges/coverage.svg)](https://codeclimate.com/github/shipshapecode/angular-shepherd/coverage)
[![Greenkeeper badge](https://badges.greenkeeper.io/shipshapecode/angular-shepherd.svg)](https://greenkeeper.io/)

This is an Angular wrapper for the [Shepherd](https://github.com/shipshapecode/shepherd), site tour, library. 
It provides additional functionality, on top of Shepherd, as well.

## Compatibility

This has not been tested in anything but Angular 8+. It may or may not work in previous versions or subsequent versions
of Angular. We would love to support multiple versions, if people with more Angular knowledge would be willing to help us out!

## Installation

```bash
npm install angular-shepherd --save
```

## Usage

**NOTE: This may not be the proper Angular way to do everything, as I am not
an Angular dev, so please let me know if you have suggestions!**

Shepherd ships a single style file, which you will need to include. You can do so by adding it
to your angular.json.

```json
  "styles": [
     "node_modules/shepherd.js/dist/css/shepherd.css"
  ],
```

Then, you will need to inject the `ShepherdService` to be able to interact with Shepherd and 
call `addSteps` to add your steps, `start` to start the tour, etc.

You could either do this at the application level, in your application component
or on a per component or per route/view basis.

In that component you will want to use `AfterViewInit` to `addSteps` to the Shepherd service.

```typescript
import { Component, AfterViewInit } from '@angular/core';
import { ShepherdService } from 'angular-shepherd';
import { steps as defaultSteps, defaultStepOptions} from '../data';

@Component({
  selector: 'shepherd',
  templateUrl: './shepherd.component.html',
  styleUrls: ['./shepherd.component.css']
})
export class ShepherdComponent implements AfterViewInit {

  constructor(private shepherdService: ShepherdService) { }

  ngAfterViewInit() {
    this.shepherdService.defaultStepOptions = defaultStepOptions;
    this.shepherdService.modal = true;
    this.shepherdService.confirmCancel = false;
    this.shepherdService.addSteps(defaultSteps);
    this.shepherdService.start();
  }
}
```

## Configuration

The following configuration options can be set on the ShepherdService to control the way that Shepherd is used. 
**The only required option is `steps`, which is set via `addSteps`.**

### confirmCancel

`confirmCancel` is a boolean flag, when set to `true` it will pop up a native browser
confirm window on cancel, to ensure you want to cancel.

### confirmCancelMessage

`confirmCancelMessage` is a string to display in the confirm dialog when `confirmCancel`
is set to true.

### defaultStepOptions

`defaultStepOptions` is used to set the options that will be applied to each step by default.
You can pass in any of the options that you can with Shepherd.

**⚠️ You must set `defaultStepOptions` *BEFORE* calling `addSteps` to set the steps.**

It will be an object of a form something like:

```js
this.shepherdService.defaultStepOptions = {
  classes: 'custom-class-name-1 custom-class-name-2',
  scrollTo: false,
  cancelIcon: {
    enabled: true
  }
};
```

> **default value:** `{}`


### requiredElements

`requiredElements` is an array of objects that indicate DOM elements that are **REQUIRED** by your tour and must
exist and be visible for the tour to start. If any elements are not present, it will keep the tour from starting.

You can also specify a message, which will tell the user what they need to do to make the tour work.

**⚠️ You must set `requiredElements` *BEFORE* calling `addSteps` to set the steps.**

_Example_
```js
this.shepherdService.requiredElements = [
  {
    selector: '.search-result-element',
    message: 'No search results found. Please execute another search, and try to start the tour again.',
    title: 'No results'
  },
  {
    selector: '.username-element',
    message: 'User not logged in, please log in to start this tour.',
    title: 'Please login'
  },
];
```

> **default value:** `[]`

### modal

`modal` is a boolean, that should be set to true, if you would like the rest of the screen, other than the current element, 
greyed out, and the current element highlighted. If you do not need modal functionality, you can remove this option or set it to false.

> **default value:** `false`

### addSteps

You must pass an array of steps to `addSteps`, something like this:

```js
this.shepherdService.addSteps([
  {
    id: 'intro',
    attachTo: { 
      element: '.first-element', 
      on: 'bottom'
    },
    beforeShowPromise: function() {
      return new Promise(function(resolve) {
        setTimeout(function() {
          window.scrollTo(0, 0);
          resolve();
        }, 500);
      });
    },
    buttons: [
      {
        classes: 'shepherd-button-secondary',
        text: 'Exit',
        type: 'cancel'
      },
      {
        classes: 'shepherd-button-primary',
        text: 'Back',
        type: 'back'
      },
      {
        classes: 'shepherd-button-primary',
        text: 'Next',
        type: 'next'
      }
    ],
    cancelIcon: {
      enabled: true
    },
    classes: 'custom-class-name-1 custom-class-name-2',
    highlightClass: 'highlight',
    scrollTo: false,
    title: 'Welcome to Angular-Shepherd!',
    text: ['Angular-Shepherd is a JavaScript library for guiding users through your Angular app.'],
    when: {
      show: () => {
        console.log('show step');
      },
      hide: () => {
        console.log('hide step');
      }
    }
  },
  ...
]);
```

## Buttons

In Shepherd, you can have as many buttons as you want inside a step. You can build an object with some premade buttons, making it easier to manipulate and insert in new steps. Buttons by default accept three different types: back, cancel, next.

```js
const builtInButtons = {
  cancel: {
    classes: "cancel-button",
    text: "cancel",
  },
  next: {
    classes: "next-button",
    text: "Next",
    type: "next"
  },
  back: {
    classes: "back-button",
    secondary: true,
    text: "Back",
    type: "back"
  }
};
```

In this simple example, we have three buttons: each one with different types and classes. 

**⚠️ You can't set up a type and an action at the same time inside a button**. 

There are some techniques to make certain buttons perform the actions you want on the component. In Angular, you can use Decorators to make some actions work by events. For example, you can listen for document clicks (mouse events) and check what the user is doing.

First, you need to import HostListener from @angular/core. 

```js
import { HostListener } from '@angular/core';
```

Now, inside your component, it's possible to use HostListener decorator inside your component, as simple as the code below.

```js
@HostListener('document:click', ['$event.target'])
onActionHandler() {
    console.log(event);
}
```

With this decorator and function, `onActionHandler` will run every time and `document:click` event occurs. A quick way to watch for specific tutorial buttons clicks is declaring a class for that button. The default button class declaration for shepherd buttons is: "*type-class custom-class shepherd-button*". 

For example, a button with type next and a custom class will be: "*next-button custom-class shepherd button*". So if you use some event handler and check `classList` array, the second element will be your custom class. Let's see how we can achieve this:

```js
  doSomething() {
    console.log('Custom class button clicked!');
  }

  @HostListener('document:click', ['$event.target'])
  onActionHandler() {
    var element = event.target as HTMLButtonElement;
    if (event.type == 'click' && element.classList[1] == 'custom-class-button') {
      this.doSomething();
    } 
  }
```
With this, whenever a button with *custom-class-button* is clicked, `doSomething` is called, printing “Custom class button clicked!”.

## Step Options

See the [Step docs](https://shepherdjs.dev/docs/Step.html) for all available Step options.
