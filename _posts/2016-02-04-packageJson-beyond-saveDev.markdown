---
layout: post
title: "First!: 2 cool things about npm's package.json"
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
