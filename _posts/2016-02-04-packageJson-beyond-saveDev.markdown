---
layout: post
title: "001: The main attribute in a package.json"
date: 2016-02-04
categories: development javascript npm
---

As the inaugural blog post of DFP Banchan, I feel like I should take this opportunity to explain why I even started a blog in the first place.

As I learn more about programming and the different utilities out there, I want to make sure that I'm learning things thoroughly, and the only way I feel
that I would be able to do that is by absorbing the material as if I were about to present or teach it to someone else (in this case, in the form of a
blog post). My hope is that, in preparing and writing these blog posts, I'll wind up doing projects and code samples (reinforcing what I've learned)
in order to supplement the material.

- - -

#### The `package.json` `main` attribute

One thing that always confused me in the node.js and npm ecosystem was the `package.json` that seems to accompany every express and JavaScript project
as well as the role it played (if any) in bundling up your project as an npm to be `require`'d in another project.

First of all, let's look at an average `package.json` file:

{% highlight json %}
{
  ...
  "name": "farm-cow",
  "main": "index.js"
  ...
}
{% endhighlight %}

Where `index.js` may look something like this:

{% highlight javascript %}
  // index.js
  function FarmCow() {
    this.moo = function() {
      console.log('Moooooo-o-o-o!');
    }
  }

  module.exports = FarmCow;
{% endhighlight %}

What those two attributes in the `package.json` are saying is that:

  - when you're trying to require the npm that I'm bundled with, *use my name attribute* (in this case, `farm-cow`)
  - when you do require me, the only API that I will expose will be whatever is exported in the file that my `main` attribute is set to.

Thus, given the above 2 files as an npm, you'd get the following:

{% highlight javascript %}
  var FarmCow = require('farm-cow');
  FarmCow
  // => [Function: FarmCow]
{% endhighlight %}

- - -

#### So, what did we learn?

  - The `package.json` serves several very important purposes but the most important ones are setting the module name with which it gets 
    required by in other projects.
  - Set the `main` attribute to the single JS file that serves as the entry-point to your module.
  - Ensure that this JS file exposes only the pieces that you want it to, via tailoring the assignment of `module.exports` to your needs.

Now go forth and write highly modularized and de-coupled code!

-David
