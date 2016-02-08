---
layout: post
title: "003: Ractive Partials Vol.1: How To Make 'Em"
date: 2016-02-07
categories: development javascript ractive
---

#### How to create Partials
There are 2 ways to create a partial in Ractive, and which one to choose all boils down to whether you want to:

* pre-parse the partial HTML OR
* declare the partial inline in the document

##### Preparsing the HTML
This is probably the most conventional way to insert partials in most front-end work when you know what has to be
placed in the document before render, and that structure won't have to change due to user interaction or the like.

{% highlight javascript %}
  var template = [
    '<section id="template">',
    {% raw %}
    '{{>headlineFoo}}',
    {% endraw %}
    '</section>'
  ].join('');
{% endhighlight %}

The above declaration, in Ractive terms, is a string representation of a Ractive template that will call on a `headlineFoo` partial to be 
inserted into the block from which it's called. Why this naming is important will be revealed soon. Note the `>`
character to denote the insertion of a partial in a template.

{% highlight javascript %}
  var headlinePartial = [
    '<div>',
    {% raw %}
    '<h1>{{headline}}</h1>',
    {% endraw %}
    '</div>'
  ].join('');
{% endhighlight %}

The above code is a string representation of a partial with a data interpolation in it (note the curly brace surrounded headline attribute).

#### Uniting the Partial and the Template
Now that we have these two autonomous pieces (the template and the partial that it contains), we can make a call to
`new Ractive()` to bind these together and insert it into a container or element of our choosing.

{% highlight javascript %}
  var ractive = new Ractive({
    el: '#container',
    template: template,
    partials: { headlineFoo: headlinePartial },
    data: { headline: 'Foobar headline' }
  });
{% endhighlight %}

Let me explain each element of the object passed into the `new Ractive()` call:

* `el` is a string of the CSS selector representation of the element that the final, rendered HTML should be inserted into.
* `template` is a string/parsed representation of the HTML template
* `partials` is an object whose keys are the names of the partials that you want to be made available to the template and whose
  values are the string/parsed HTML that contain the contents of the partial.
  * One thing to note here is that the name of the keys, like keys in a `data` object, must match the name that they are invoked
    with later on in the template or partial.
* `data` is an object whose keys are the names of different data elements in the partial OR the template. These keys can be called in curly
  braces within the template or partial and have their data interpolated into the HTML at that location.

#### The globally available `partials` object
Ractive also makes a globally available `partials` object, which can be found with a call to `Ractive.partials`

An HTML string can be set to the value of a key in this object and can later be called by a call to `new Ractive()` without having to
pass in a `partials` object, as long as the template only calls partials that are globally available. Any partials
that are not globally available will, of course, have to be passed in.

{% highlight javascript %}
  $(document).ready(function() {
    Ractive.partials.headlineFoo = [
      '<div>',
      {% raw %}
      '<h1>{{headline}}</h1>',
      {% endraw %}
      '</div>'
    ].join('');

    var template = [
      '<section id="template">',
      {% raw %}
      '{{>headlineFoo}}',
      {% endraw %}
      '</section>'
    ].join('');

    var ractive = new Ractive({
      el: '#foo .container',
      template: template,
      data: { headline: 'Foobar headline' }
    });
  });
{% endhighlight %}

In this case, Ractive doesn't need a `partials` object to determine which partial to insert as a partial with the name `headlineFoo` has
been made available globally.

#### `script` tag Partials
Ractive makes yet another method to insert partials available, by allowing inline partial declaration via a `script` tag with the `id` attribute
set to the name of the partial that the template will eventually look for.

The following code would also produce the same results:

{% highlight javascript %}
  var template = [
    '<section id="template">',
    {% raw %}
    '{{>headlineFoo}}',
    {% endraw %}
    '</section>'
  ].join('');

  var ractive = new Ractive({
    el: '#foo .container',
    template: template,
    data: { headline: 'Foobar headline' }
  });
{% endhighlight %}

{%  highlight html %}
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="UTF-8">
      <title></title>
      <script src="public/javascripts/app.js"></script>
    </head>
    <body>
      <div id="foo">
        <div class ="container"></div>
      </div>
      <div id="bar">
        <div class ="container"></div>
      </div>

      <script id="headlineFoo" type="text/ractive">
        {% raw %}
        <div><h1>{{headline}}</h1></div>
        {% endraw %}
      </script>
    </body>
  </html>
{% endhighlight %}

In this case, Ractive sees that there's a `script` tag with the id of a partial it's looking for and inserts that into the template.
Note that the type attribute in this case is entirely optional, but the Chrome console will throw a parsing error if it isn't there.

#### Inline partials

The final way that a Ractive partial can be declared is inline in the actual template file. In this method, we declare the template
by denoting a block with `{{#partial partialName}}` and closing it with `{{/partial}}`. Take care to name the partial the same as what
it's called by when you call it in the template.

{% highlight javascript %}
  $(document).ready(function() {
    var template = [
      {% raw %}
      '<section id="template">',
      '{{>headlineFoo}}', // partial name
      '</section>',
      '{{#partial headlineFoo}}', // matching partial name
      '<h1>{{headline}}</h1>',
      '{{/partial}}'
      {% endraw %}
    ].join('');

    var ractive = new Ractive({
      el: '#foo .container',
      template: template,
      data: { headline: 'Foobar headline' }
    });
  });
{% endhighlight %}

Up next, the different stuff you can do with partials in Ractive!
