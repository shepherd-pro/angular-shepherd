# angular-shepherd

<a href="https://shipshape.io/"><img src="http://i.imgur.com/DWHQjA5.png" alt="Ship Shape" width="100" height="100"/></a>

**[angular-shepherd is built and maintained by Ship Shape. Contact us for web app consulting, development, and training for your project](https://shipshape.io/)**.

[![npm version](https://badge.fury.io/js/angular-shepherd.svg)](http://badge.fury.io/js/angular-shepherd)
![Download count all time](https://img.shields.io/npm/dt/angular-shepherd.svg)
[![npm](https://img.shields.io/npm/dm/angular-shepherd.svg)]()
![CI Build](https://github.com/shipshapecode/angular-shepherd/workflows/CI%20Build/badge.svg)

This is an Angular wrapper for the [Shepherd](https://github.com/shipshapecode/shepherd), site tour, library. 
It provides additional functionality, on top of Shepherd, as well.

## Compatibility

* Angular 8: 0.5.0
* Angular 9: 0.6.0
* Angular 10: 0.7.0
* Angular 11: 11.x
* Angular 12: 12.x
* Angular 13: 13.x
* Angular 14: 14.x
* Angular 15: 15.x
* Angular 16: 16.x
* Angular 17: 17.x

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

**NOTE: It is highly recommended to inject ShepherdService into your `app.component.ts`. Injecting it
at the app level ensures you only create one instance of Shepherd.**

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

In Shepherd, you can have as many buttons as you want inside a step. You can build an object with some premade buttons, making it easier to manipulate and insert in new steps. Buttons by default accept three different types: back, cancel, next. In this simple example, we have three buttons: each one with different types and classes. 

```js
const builtInButtons = {
  cancel: {
    classes: "cancel-button",
    text: "Cancel",
    type: "cancel"
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

Buttons have an action property, which must be a function. Whenever the button is clicked, the function will be executed. You can use it for default shepherd functions, like `this.shepherdService.complete()` or `this.shepherdService.next()`, or create your own function to use for the action.

```js
const builtInButtons = {
  complete: {
    classes: "complete-button",
    text: "Finish Tutorial",
    action: function() {
      return console.log('button clicked');
    }
  }
};
```

**⚠️ You can't set up a type and an action at the same time inside a button**.

To learn more about button properties, look at the [documentation](https://docs.shepherdpro.com/api/step/classes/step/).

## Step Options

See the [Step docs](https://docs.shepherdpro.com/api/step/classes/step/) for all available Step options.
