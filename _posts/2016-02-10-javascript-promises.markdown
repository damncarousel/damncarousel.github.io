---
layout: post
title: "006: JavaScript Promises in a Nutshell"
date: 2016-02-10
categories: development javascript promises
---

If you've written Javascript for any appreciable amount of time, there's a very real possibility that you've
run into what JS developers affectionately call "callback hell".

Callback hell is basically what happens when you have a bunch of asynchronous function calls nested within each
other so that the next async function knows what it has to do when the result finally comes back. It looks
something like this:

{% highlight javascript %}
  function() {
    // do some synchronous stuff here

    asyncFn1(function() {
      // some synchronous stuff that should 
      // be done with the results of asyncFn1

      asyncFn2(function() {
        // some synchronous stuff that should 
        // be done with the results of asyncFn2

        asyncFn3(function() {
          // some synchronous stuff that should 
          // be done with the results of asyncFn3
        });
      });
    });
  }
{% endhighlight %}

You could obviously see how this would get completely out of hand and super difficult to parse.

#### Promises to the rescue!

Promises have been natively baked into Javascript to try and combat this callback hell problem. They at least
make them easier to reason about and to read. They are comprised of several basic concepts, which I'll outline
for you right now:

* Resolve/Reject function
  * The resolve/reject function is a function that outlines what criteria that must be fulfilled in order for a
    promise to be considered resolved and then makes the call to `resolve()` (as well as the call to `reject()`)
    if the criteria has not been met).

An example resolve/reject function could look like this:

{% highlight javascript %}
  function(resolve, reject) {
    setTimeout(function() {
      if (someCriteria === isMet) {
        resolve('Hurray, the promise has been resolved!');
      } else {
        reject(':-( Too bad...');
      }
    }, 4000);
  }
{% endhighlight %}

This function declaration takes two parameters, `resolve` and `reject` that represents exactly that. After
determining whether or not certain criteria are met for resolution or rejection, one of those two functions are
called. The order of these two parameters is important, as you'll see later on, because they determine which
callback function is executed.

#### Instantiating the Promise
Once your resolver function has been declared, it can be passed into a `new Promise()` invocation, that returns 
an instance of a promise. The following code illustrates this:

{% highlight javascript %}
  var promiseCount = 0;

  function createPromise() {
    var thisPromiseCount = ++promiseCount;

    return new Promise(function(resolve, reject) {
      setTimeout(function() {
        if (thisPromiseCount % 2 === 0) {
          resolve('Success! An Even Number!'); 
        } else {
          reject('Fail! This number is odd!');
        }
      }, 4000);
    });
  }
{% endhighlight %}

What the function `createPromise()` does is instantiates a Promise and provides it a resolver function that (after 4 seconds)
checks whether or not the current value of `thisPromiseCount` is even. If it is even, it successfully resolves the promise with
a string of `Success! An Even Number!`. If not, it rejects the promise with a failure message.

#### Putting it all together

Now that we've outlined a Promise and it's criteria for fulfillment, we can then outline what it should do with the information that
is returned from the asynchronous function. This is where the `then()` and `catch()` calls would come in.

Both the `then()` and `catch()` invocations accept a function that details what should be done in each scenario with the parameter that
was passed to it in the earlier calls to `resolve()` or `reject()`. The following code illustrates, how they should be written:

{% highlight javascript %}
  // Let's invoke the function that returns the Promise
  createPromise()
    .then(function(val) { // This promise responds to a then() method that accepts a function
      console.log(val); // This is executed if the promise invokes the resolve() method
    })
    .catch(function(val) { // The catch() also accepts a function
      console.log(val); // This is executed if the promise invokes the reject() method
    });
{% endhighlight %}

Each time the above is called, the value of `thisPromiseCount` will be evaluated, and the proper callback gets executed
depending on whether the Promise was resolved or rejected.
