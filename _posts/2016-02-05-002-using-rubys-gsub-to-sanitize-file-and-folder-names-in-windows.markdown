---
layout: post
title: "002: Using Ruby's gsub method to sanitize file and folder names for Windows"
date: 2016-02-05
categories: development ruby gsub regex
---

A problem I'm sure tons of Windows users have run into in the past is the OS not allowing you to use certain characters when
naming a file or folders. The current (as of this writing) list of prohibited characters in NTFS (the file system used by most 
modern Windows OSes) is as follows:

`? * " / \ > < * | :`

Because of the low cost of Windows machines as well as other programs that we have to use here at work, we've decided to go with
Windows PC's in order to perform any copying and folder creation related to our audio assets. Yours truly built a script/cli for that
awhile back and it has come time to start sanitizing these characters from within the script in order to prevent the issue of 
copy operations failing because Ruby can't either create the folder or save to a specific filename.

In order to do this, we're going to use Ruby's `String#gsub()` method and a regex that targets every occurrence of the offending characters
and replaces them with a single whitespace character.

- - -

#### The `String#gsub()` method

The first argument to the `gsub()` method can be one of two things:
* a string literal: In this case, the method searches the string for any occurrence of the string passed to it, and replaces it with the 2nd parameter.
* a regular expression: In this case, the method searches for parts of the string that match the regular expression, and replaces them with the 2nd parameter.

The second argument to `gsub()` method can also be one of two things:

* a string literal: pretty self-explanatory. If the `replacement` argument is a string, it will simply replace whatever is matched with the `replacement`.
* a block: this is where things get fun. If you pass a block to `gsub` as it's second argument, it will allow you to act on whatever is matched.
  For example:

  {% highlight ruby %}
    str = "Who Am I?" 
    str.gsub "Who" do |match|
      # match, in this scope, is every occurrence of "Who" in str
      match.upcase
    end

    #=> "WHO Am I?"
  {% endhighlight %}

#### Regular Expressions

For our purposes, what we need is a regular expression that will match every individual occurrence of any of the characters listed above, regardless
of their position in the string and replace them with a single whitespace character. Thus it should turn the following string:

6*9 W?er> Am I?

into this string:

6 9 W er  Am I?

The proper regular expression for this would then be:

`[?*"\/\\><|:]` 

taking proper care to both:

* escape any reserved characters with a backslash (the forward and backslash characters)
* enclose the regex with square brackets to ensure that regex looks to match only a single character within the entire array of characters, rather than
  to match an all-at-once occurrence of `?*"/\><|:`

Testing in an `irb` console shows that the regular expression does exactly what we want it to:

{% highlight ruby %}
  str = "I Am : An Inva|id Folder Name?"
  str.gsub(/[?*"\/\\><|:]/, " ")

  #=> "I Am   An Inval d Folder Name "
{% endhighlight %}

- - -

#### So, what have we learned?

* The `String#gsub()` method signature can take either a string or regex as an input, do a match, and then either replace or act on the match, depending
  on whether or not the 2nd argument is a string or a block.
* Unless a regular expression is enclosed in square brackets, the regex will try to match the string in it's entirety and not just a single character within
  the string.

Take these tools and Regex on!

-David
