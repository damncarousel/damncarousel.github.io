---
layout: post
title: "004: ES6 hashrocket functions and this"
date: 2016-02-08
categories: development javascript es6 hashrocket
---

The value of `this` and what it is depending on where it is has always been a point of annoyance with anyone
writing Javascript and nesting a function within a function. ECMAscript 6 has provided a much simpler syntax
to allow for the lexical binding of `this` so you can keep the context of it regardless of how deep you are
within a nested a function.

##### An illustration of the problem

In the conventional way of writing a function in JS, one might write code like this:

{% highlight javascript %}
  var Person = function() {
    this.age = 0;
    
    this.myFunc = function() {
      console.log(this);
    }
  }

  var p = new Person();
  p.myFunc();
  //=> { age: 0, myFunc: [Function] }
{% endhighlight %}

As one might expect, when `this` is logged to the console in the above code, we get an instance of `Person`
returned to us.

However, if we nest and return another function, we see the issue:

{% highlight javascript %}
  var Person = function() {
    this.age = 0;
    
    this.myFunc = function() {
      // Here, this still refers to the current instance of Person

      return function() {
        // Here, this now refers to the global object
        console.log(this);
      }
    }
  }

  var p = new Person();
  (p.myFunc())();
  //=> some crazy, giant javascript object that represent the global `this` object.

{% endhighlight %}

As you can see here, after the call to `this` gets nested within another function call, it completely loses it's
context and gets reset to the global scope (because we aren't using call or apply);

##### Hashrocket to the rescue!

As I mentioned earlier, ES6 provides the hashrocket function declaration syntax and using this
successfully preserves the scope of `this`.

An illustration of this:

{% highlight javascript %}
  var Person = function() {
    this.age = 0;
    
    this.myFunc = function() {
      // Here, this still refers to the current instance of Person

      return () => {
        // Here, because of the hashrocket syntax, the value of this 
        // is retained as the current instance of person.
        console.log(this);
      }
    }
  }

  var p = new Person();
  (p.myFunc())();
  //=> { age: 0, myFunc: [Function] }
{% endhighlight %}

Make sure that if, for whatever reason, you have to nest ANOTHER function within the nested function that you
again write the function declaration with the hashrocket syntax, as another nested function will again reset
the value of `this` to the global scope.
