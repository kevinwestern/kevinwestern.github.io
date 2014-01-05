---
layout: post
title: "CSS3: An Introduction To Transitions"
date: 2012-08-19 10:01
comments: true
categories: [CSS3, CSS, webdev]
published: true
---

Part of CSS3's features that have a lot of popularity behind them are its transitions, transformations and animations. We're no longer dependent on a JavaScript library providing basic animations; we can let the browser do the heavy lifting for us. In my experience, popular JS animations have been simple transitions. In this post I'll explore how to get started using CSS3's transitions.




## Transition

CSS3's transition property is known simply as "transition". However, because the transition spec is still a working copy, we'll need to use browser prefixes. Let's look at a quick example:

### Example

{% highlight css %}
#example {
    width: 75px;
    height: 75px;
    background-color: black;
    transition-property: all;
    transition-duration: 2s;
    transition-timing-function: ease-in;
    transition-delay: 0.5s
}

#example:hover {
    background-color: red
}
{% endhighlight %}

<div id='example'>
   Hover
</div>


### Breakdown
The code may appear intimidating but fear not! It looks like a lot of code but it's really not. The extra browser prefixes adds a lot of bloat. You should be using a CSS preprocessor such as [compass](http://compass-style.org/), [less](http://lesscss.org/) or [stylus](http://learnboost.github.com/stylus/) to write these prefixes for you.

##### transition-property
The first group of transition styles target the 'transition-property'. The 'transition-property' field denotes which css property you'd like transition to apply to. For example, I could have specified 'background-color' instead of 'all' to achieve the same effect in this scenario. Having a value of 'all' will apply the transition when any other css property changes. See [animatable properties](http://www.w3.org/TR/css3-transitions/#animatable-properties) in the CSS3 Transition working draft for a list of css properties that can be animated.

##### transition-duration
The second group of transition styles specify how long the transition should last.

##### transition-timing-function
The third group, 'transition-timing-function' property, describes how the intermediate values between the start and end of the transition will be calculated. Another word for this effect can be called the 'easing' function. A list of timing functions have already been defined for us: ease, linear, ease-in, ease-out, ease-in-out, step-start, step-end. See what the different effects look like [here]({{ root_url }}/blog/2012/08/19/css3-transition-timing-functions/). These timing functions are built using a stepping function or a [cubic Bezier curve](http://en.wikipedia.org/wiki/B%C3%A9zier_curve#Cubic_B.C3.A9zier_curves). Read more about timing functions [here](http://www.w3.org/TR/css3-transitions/#transition-timing-function-property).

##### transition-delay
The fourth and final property we'll look at is the 'transition-delay' property. This property simply states how long the transition should wait, or be delayed, until the transition starts.


##### transition shorthand notation
I've used the longhand notation for this example to allow for easy explanation. We can reduce the example to shorthand notation in the following format:

{% highlight html %}
transition: <property> <duration> <timing-function> <delay> 
{% endhighlight %}

Using this notation the example above would look like the following:

{% highlight css %}
#example {
    width: 75px;
    height: 75px;
    background-color: black;
    transition: all 2s ease-in-out 0.5s
}

#example:hover {
   background-color: red;
}
{% endhighlight %}
Read more about [shorthand notation](http://www.w3.org/TR/css3-transitions/#transition-shorthand-property).

CSS3 allows for simple and elegant transitions which otherwise would require extra JavaScript code and testing. With better browser adoption and even mobile adoption of CSS3 I'm excited to drop JS animation libraries.