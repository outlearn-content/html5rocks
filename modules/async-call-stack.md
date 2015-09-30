<!--
{
"name" : "async-call-stack",
"version" : "0.1",
"title" : "Debugging Asynchronous JavaScript with Chrome DevTools",
"description" : "Assigning async callbacks lets you write event-driven code but it also makes tracking down bugs a hair pulling experience since the JavaScript is not executing in a linear fashion. Luckily, now in Chrome DevTools, you can view the full call stack of asynchronous JavaScript callbacks!",
"freshnessDate" : 2014-07-04,
"homepage" : "http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/",
"canonicalSource" : "http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/",
"author" : "Pearl Chen",
"license" : "Content - CC BY 3.0, Code - Apache 2.0"
}
-->


<!-- @section -->
##Introduction

A powerful feature that makes JavaScript unique is its ability to work asynchronously via callback functions. Assigning async callbacks let you write event-driven code but it also makes tracking down bugs a hair pulling experience since the JavaScript is not executing in a linear fashion.

Luckily, now in Chrome DevTools, you can view the **full** call stack of asynchronous JavaScript callbacks!

![A quick teaser overview of async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/xhr-async.gif)

**_A quick teaser overview of async call stacks._**  
(We'll break down the flow of this demo soon.)

Once you enable the async call stack feature in DevTools, you will be able to drill into the state of your web app at various points in time. Walk the full stack trace for some event listeners, `setInterval`, `setTimeout`, `XMLHttpRequest`, promises, `requestAnimationFrame`, `MutationObservers`, and more.

As you walk the stack trace, you can also analyze the value of any variable at that particular point of runtime execution. It's like a time machine for your watch expressions!

Let's enable this feature and take a look at a few of these scenarios.

<!-- @section -->
##Enable async debugging in Chrome

Try out this new feature by enabling it in Chrome. Go to the **Sources** panel of Chrome Canary DevTools.

Next to the **Call Stack** panel on the right hand side, there is a new checkbox for "Async". Toggle the checkbox to turn async debugging on or off. (Although once it's on, you may not ever want to turn it off.)

![Toggle the async feature on or off](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/enable-async-toggle.gif)

<!-- @section -->
##Capture delayed timer events and XHR responses

You've probably seen this before in Gmail:

![Gmail retrying to send an email](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/gmail-retrying.png)

If there is a problem sending the request (either the server is having problems or there are network connectivity issues on the client side), Gmail will automatically try re-sending the message after a short timeout.

To see how async call stacks can help us analyze delayed timer events and XHR responses, I've recreated that flow with a [mock Gmail example](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/xhr/demo.html). The full JavaScript code can be found in the link above but the flow is as follows:

![Flow chart of mock Gmail example](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/xhr-async-flow.gif)

**_In the diagram above, the methods highlighted in blue are prime spots for this new DevTool feature to be the most beneficial since these methods work asynchronously._**

By solely looking at the Call Stack panel in previous versions of DevTools, a breakpoint within `postOnFail()` would give you little information about where `postOnFail()` was being called from. But look at the difference when turning on async stacks:

#### Before

![Breakpoint set in mock Gmail example without async call
 stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/xhr-before.gif)

**_The Call Stack panel_ without _async enabled._**  
Here you can see that `postOnFail()` was initiated from an AJAX callback but no further info.

#### After

 ![Breakpoint set in mock Gmail example with async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/xhr-after-click.gif)

**_The Call Stack panel_ with _async enabled._**  
Here you can see that the XHR was initiated from `submitHandler()`. Nice!

With async call stacks turned on, you can view the entire call stack to easily see if the request was initiated from `submitHandler()` (which happens after clicking the submit button) or from `retrySubmit()` (which happens after a `setTimeout()` delay):

#### submitHandler()

![Breakpoint set in mock Gmail example with async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/xhr-after-click.gif)

#### retrySubmit()

![Another breakpoint set in mock Gmail example with async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/xhr-after-retry-shortened.gif)

<!-- @section -->
##Watch expressions asynchronously

When you walk the full call stack, your watched expressions will also update to reflect the state that it was in at that time!

![An example of using watch expressions with aysnc call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/watch-expressions.gif)

<!-- @section -->
##Evaluate code from past scopes

In addition to simply *watching* expressions, you can interact with your code from previous scopes right in the DevTools JavaScript console panel.

Imagine that you are Dr. Who and you need a little help comparing the clock from before you got into the Tardis to "now". From the DevTools console, you can easily evaluate, store, and do calculations on values from across different execution points.

![An example of using the JavaScript console with aysnc call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/time-travel.gif)

**_Use the JavaScript console in conjunction with async call stacks to debug your code. The above demo can be found [here](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/past-scope/demo.html)._**

Staying within DevTools to manipulate your expressions will save you time from having to switch back to your source code, make edits, and refresh the browser.

<!-- @section -->
##Unravel chained promise resolutions

If you thought the previous mock Gmail flow was hard to unravel without the async call stack feature enabled, can you imagine how much harder it would be with more complex asynchronous flows like chained promises? Let's revisit the final example of Jake Archibald's tutorial on [JavaScript Promises.](http://www.html5rocks.com/en/tutorials/es6/promises/)

![Promise-flow](https://raw.githubusercontent.com/outlearn-content/html5rocks/master/assets/promise-flow.png)

**_Flow diagram from_ JavaScript Promises.**

Here's a little animation of walking the call stacks in Jake's [async-best-example.html](http://www.html5rocks.com/en/tutorials/es6/promises/async-best-example.html) example.

#### Before

![Breakpoint set in promises example without async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/promises-before.gif)

**_The Call Stack panel_ without _async enabled._**  
Notice how the Call Stack panel is pretty short on info when trying to debug promises.

#### After

![Breakpoint set in promises example with async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/promises-after.gif)

**_The Call Stack panel_ with _async enabled._**  
Wow! Such promises. Much callbacks.

<!-- @section -->
##Get insights into your web animations

Let's go deeper into the HTML5Rocks archives. Remember Paul Lewis' *[Leaner, Meaner, Faster Animations with requestAnimationFrame](http://www.html5rocks.com/en/tutorials/speed/animations/)?*

Open up the [requestAnimationFrame demo](http://www.html5rocks.com/en/tutorials/speed/animations/post.html) and add a breakpoint at the beginning of the `update()` method (around line 874) of post.html. With async call stacks we get a lot more insights into `requestAnimationFrame`, including the ability to walk all the way back to the initiating scroll event callback.

#### Before

![Breakpoint set in requestAnimationFrame example without async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/requestAnimationFrame-before.gif)

**_The Call Stack panel_ without _async enabled._**

#### After

![Breakpoint set in requestAnimationFrame example with async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/requestAnimationFrame-after.gif)

**_And_ with _async enabled._**

<!-- @section -->
##Track down DOM updates when using MutationObserver

`MutationObserver` allow us to observe changes in the DOM. In this [simple example](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/mutation-observers/demo.html), when you click on the button, a new DOM node is appended to ` <div class="rows"></div>`.

Add a breakpoint within `nodeAdded()` (line 31) in demo.html. With async call stacks enabled, you can now walk the call stack back through `addNode()` to the initial click event.

#### Before

![Breakpoint set in mutationObserver example without async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/mutationObserver-before.gif)

**_The Call Stack panel_ without _async enabled._**

#### After

![Breakpoint set in mutationObserver example with async call stacks](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/mutationObserver-after.gif)

**_And_ with _async enabled._**

<!-- @section -->
##Tips for debugging JavaScript in async call stacks

### Name your functions

If you tend to assign all of your callbacks as anonymous functions, you may wish to instead give them a name to make viewing the call stack easier.

For example, take an anonymous function like this:

``` javascript
window.addEventListener('load', function(){
  // do something
});
```

And give it a name like `windowLoaded()`:

``` javascript
window.addEventListener('load', function windowLoaded(){
  // do something
});
```

When the load event fires, it will show up in the DevTools stack trace with its function name instead of the cryptic "*(anonymous function)*". This makes it much easier to see at a glance what's happening in your stack trace.

#### Before

![An anonymous function](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/function-names-before.gif)

#### After

![A named function](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/function-names-after.gif)

<!-- @section -->
##Explore further

To recap, these are all the asynchronous callbacks in which DevTools will display the full call stack:

-   **Timers**: Walk back to where `setTimeout()` or `setInterval()` was initialized.
-   **XHRs**: Walk back to where `xhr.send()` was called.
-   **Animation frames**: Walk back to where requestAnimationFrame was called.
-   **Promises**: Walk back to where a promise has been resolved.
-   **Object.observe**: Walk back to where the observer callback was originally bound.
-   **MutationObservers**: Walk back to where the mutation observer event was fired.
-   **window.postMessage()**: Walk over intra-process messaging calls.
-   **DataTransferItem.getAsString()**
-   **FileSystem API**
-   **IndexedDB**
-   **WebSQL**
-   **Eligible DOM events via `addEventListener()`**: Walk back to where the event was fired. Due to performance reasons, not all DOM events are eligible for the async call stacks feature. Examples of currently available events include: 'scroll', 'hashchange', and 'selectionchange'.
-   **Multimedia events via `addEventListener()`**: Walk back to where the event was fired. Available multimedia events include: audio and video events (e.g. 'play', 'pause', 'ratechange'), WebRTC MediaStreamTrackList events (e.g. 'addtrack', 'removetrack'), and MediaSource events (e.g. 'sourceopen').

Being able to see the full stack trace of your JavaScript callbacks should keep those hairs on your head. This feature in DevTools will be especially helpful when multiple async events happen in relation to each other, or if an uncaught exception is thrown from within an async callback.

Give it a try in Chrome. If you have feedback on this new feature, drop us a line on the Chrome DevTools [bug tracker](https://code.google.com/p/chromium/issues/detail?id=272416) or in the [Chrome DevTools Group](https://groups.google.com/forum/#!forum/google-chrome-developer-tools).
