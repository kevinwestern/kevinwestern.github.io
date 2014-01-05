---
layout: post
title: "CSS3: Animations"
date: 2013-03-04 20:49
comments: true
categories: [CSS3, CSS, webdev]
published: false
---

CSS3 transitions allow for simple animations but only allow control for the beginning and end of animations without giving you any choice on how they progress. Animations in CSS3 give us that control by allowing us to define how the animation will progress over time using a set of keyframes. We’re also allowed control over the frequency at which the animation executes, whether the animation is paused or running and the delay of the animation start time.


Defining an animation


As mentioned previously, animations are defined through a set of keyframes. Keyframes allow us to specify the values for the animating css property at different times throughout the animation. Keyframes use the CSS at-rule, or @keyframes, followed by a unique identifier for the set of keyframes and a set of unique selectors. This unique identifier will be the value for the ‘animation-name’ property defined on some element.

```css
@keyframes move {
 from: {
   left: 0;
   top: 0;
 },

 to: {
   left: 200px;
   top: 200px;
 }
}

#box {
  animation-name: move;
}
```

Keyframes


Keyframes are defined through keyframe selectors, which is a list of percentage values, or the words ‘from’ and ‘to' which are the same as 0% and 100% respectively. Each percentage defines when in the animation’s life cycle that frame should take effect. The frame is defined as a block of properties exactly like a normal css selector. The 

```css
#block {
    width: 50px;
    height: 50px;
    background-color: red;
    -webkit-animation-name: move;
    -webkit-animation-duration: 4s;
    -webkit-animation-iteration-count: infinite;
    -webkit-animation-direction: alternate;
    animation-name: move;
    animation-duration: 4s;
    position: absolute;
}

@-webkit-keyframes move {
    
    0%, 100% {
      top: 50px;
      left: 0;
      background-color: red;
    }
    
    25% {
      top: 0;
      left: 50px;
      background-color: green;
    }
       
    50% {
      top: 50px;
      left: 100px;
      background-color: blue;
    }
    
    75% {
      top: 100px;
      left: 50px;
      background-color: green;
    }
}
```

http://jsfiddle.net/RFhEF/4/
