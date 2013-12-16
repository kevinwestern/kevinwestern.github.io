---
layout: post
title: "CSS3: Transition Timing Functions"
date: 2012-08-19 11:42
comments: true
categories: [CSS3, CSS, webdev]
published: true
---

The CSS3 Transition spec has already defined various timing, or stepping, functions for us. What do these timing functions look like, you ask? Take a look.
<!-- more -->

<link href="{{ root_url }}/stylesheets/timing-functions.css" media="screen, projection" rel="stylesheet" type="text/css">

A list of timing functions have already been defined in the [transition-timing-function spec](http://www.w3.org/TR/css3-transitions/#transition-timing-function). Since I'm a visual guy I'd prefer to see what each of these timing functions look like with an example. Each example will move the square from left to right over 5 seconds when the container is hovered over. Learn more about [CSS transitions]({{ root_url }}/blog/2012/08/19/css3-an-introduction-to-transitions/).

<small> See the css with browser prefixes [here]({{ root_url }}/stylesheets/timing-functions.css).</small>

``` css
.square {
    width: 75px;
    height: 75px;
    background-color: black;
    position: absolute;
    left: 0;
    transition-property: left;
    transition-duration: 5s;
}
.container {
    width: 450px;
    height: 75px;
    border: solid thin black;
    margin-bottom: 30px;
    position: relative
}

.container:hover .square {
    left: 375px;
    background-color: red
}
```

### ease
``` css
#ease-transition {
	transition-timing-function: ease;
}
```
<div class='container'>
	<div id='ease-transition' class='square'>
	</div>
</div>

### linear
``` css
#linear-transition {
	transition-timing-function: linear;
}
```
<div class='container'>
	<div id='linear-transition' class='square'>
	</div>
</div>

### ease-in
``` css
#ease-in-transition {
	transition-timing-function: ease-in;
}
```
<div class='container'>
	<div id='ease-in-transition' class='square'>
	</div>
</div>

### ease-out
``` css
#ease-in-transition {
	transition-timing-function: ease-out;
}
```
<div class='container'>
	<div id='ease-out-transition' class='square'>
	</div>
</div>

### ease-in-out
``` css
#ease-in-transition {
	transition-timing-function: ease-in-out;
}
```
<div class='container'>
	<div id='ease-in-out-transition' class='square'>
	</div>
</div>

### step-start
``` css
#ease-in-transition {
	transition-timing-function: step-start;
}
```
<div class='container'>
	<div id='step-start-transition' class='square'>
	</div>
</div>

### step-end
(Hover for 5s)
``` css
#ease-in-transition {
	transition-timing-function: step-end;
}
```
<div class='container'>
	<div id='step-end-transition' class='square'>
	</div>
</div>

### steps
``` css
#ease-in-transition {
	transition-timing-function: steps(5, start);
}
```
<div class='container'>
	<div id='steps-transition' class='square'>
	</div>
</div>
