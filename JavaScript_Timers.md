# JavaScript Timers
JavaScript provides two standard timers that are available in all browsers and Node.js in identical fashion.  These were once defacto standards that were universally adopted despite not having a formal document approved by a standards body organization and achieved the status of a formal standard with HTML5.

## Timers
* [setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout) - Sets a one time millisecond delay.
* [setInterval](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval) - An infinite loop of delays.

## Cancellation
* [clearTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/clearTimeout) - Cancels a *setTimeout* operation.
* [clearInterval](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/clearInterval) - Breaks a *setInterval* loop.

## Some Quick Points
* **All timers execute asynchronously.**  They execute as a polling subprocess under the primary process executing the JavaScript interpreter.  This is fancy talk for saying timers always execute precisely to their specified delay regardless of what is occurring in the code.
* **The delay is always specified as miliseconds.**  If you wish to delay an operation by 3 seconds its delay will be specified as *3000*, which is 3 thousand milliseconds.

## Understanding the Timers
Since the delays are always asynchronous the pros and cons of each delay type are not immediately clear.  This section attempts to explore this in further detail.

### setTimeout
This is perhaps the easiest delay to understand.  This function requires two arguments: a function and a delay.  Example: `setTimeout(myFunction, 1000)`.

The primary limitation to setTimeout is that its function runs only once and its delay isn't flexible.  This means if you are waiting for part of a web page to load you must specify a far longer than needed delay because in some cases that part of the web page can load quickly while in other cases it can load slowly and you certainly don't want your code to execute too early which could cause an error.

### setInterval
Just like the setTimeout function this takes the exact same two arguments: `setInterval(myFunction, 1000)`.  The *setInterval* is more flexible than the *setTimeout* because because it creates a loop, which means its function will execute many times.

Before moving forward it is important to remember that the timers are **always asynchronous**.  In this case if the provided function is a long executing function and the specified interval is very short it is probable that the specified function will execute in the given interval before this function finished executing in the previous interval, which will introduce unexpected results.  Terms to describe this condition are *fallover* or *collision*.

One way of thinking about this is a cookie factory that drops dough onto a conveyer belt.  The dough drops onto the conveyer belt at a regular interval. The rate at which the dough drops onto the conveyer belt is not associated with the speed of the conveyer belt.  Since these operations are separately managed they are asynchronous.  The conveyer belt could slow down, but this would have no impact on how fast or regularly the dough drops onto the conveyer belt.  If the conveyer belt moves too slowly or the dough drops too quickly one instance of cookie dough will collide with the next instance of cookie dough.

The challenge with using setInterval on the web is that each interval instance is asynchronous to the JavaScript code executing around it.  Additionally, the DOM, which is the interface between the JavaScript and the web page is also asynchronous to both the JavaScript and the timers.  When describing setTimeout long delays were mentioned for safety when working with web pages, which becomes more true for each interval in the setInterval.

## Creating an asynchronous Loop - Recursive setTimeout
When working with various asynchronous factors it is generally preferable to create a loop of delays that is synchronous between each interval so as to prevent collisions or fallover.  To solve for this scenario my recommendation is a recursive setTimeout.

A recursive setTimeout is a function that contains a condition that if true calls this function to execute again in a setTimeout delay.  The benefit to a recursive setTimeout function is that it loops synchronously and this loop automatically terminates once it is no longer needed against an asynchronous test.  This means each loop iteration is synchronous compared to the previous or next interaction, but the test fired in the current iteration is asynchronous compared to other JavaScript code aside from this loop.  This delivers the best ratio of speed to safety.

Here is a brief example:

```javascript
function parent() {
    if (document.getElementById("something") !== null) {
        return setTimeout(parent, 100);
    }
    // other code follows
}
```

This concept can be reduced to an abstraction for easy use in multiple locations:

```javascript
// abstraction
function syncDelay(params) {
    if (params.test() === false) {
        return setTimeout(function syncDelay_recurse() {
            syncDelay(params);
        }, params.delay);
    }
    params.func();
}

// use case
syncDelay({
    test : function () {
        return document.getElementById("something") !== null;
    },
    func : function () {},
    delay: 50
});
```

## A Note on Speed
The standards that define these timers indicate the smallest allowed delay is 4ms, so if you specify any smaller number, such as 0, it will be executed with a delay of 4ms.  On top of that there is some minimal overhead from instantiating a timer, due to it leaving the current thread of execution for the rest of the JavaScript code to create a separate execution thread.

It isn't desirable to run timers with the minimal delay as this interferes with the speed of JavaScript performance elsewhere in the page.  In my experience a delay of 250ms is too fast to allow for human interaction, but is slow enough that a user looking at the impacted area of the page may notice a visible change, provided the timer's function visibly changes that area of the page.  250ms is slow enough that there will be no degraded performance in any other executing code, but the visual blink introduced from this speed could have consequences for things like A/B tests.

100ms is generally fast enough that humans cannot visually detect changes even if staring directly at the impacted area of the page and the timer's function dramatically changes that area of the page.  This delay is slow enough that in nearly all cases introduces no impact to performance of other executing code.  When I use timers to modify a visible page 100ms is my default delay.

There are some rare cases where it is necessary to iterate much faster than 100ms.  One such case is modifying the result of a user interaction from a third party tool.  Since our code comes from a third party tool we have no idea if the page is ready or the concerned code is there to modify, so instead we have to wrap our manipulative code in something like a recursive setTimeout and wait for the necessary pieces to be in place but before changes from the user's interaction can be detected.  In edge cases like these I have found a delay of 40ms or 50ms to be fast enough.  In complex scenarios like this the precision of the delay is becomes a competing concern to over various technical factors resulting from the manipulations to the page you don't control.