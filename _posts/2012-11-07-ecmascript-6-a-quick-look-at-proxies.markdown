---
layout: post
title: "Ecmascript 6 - Proxies At A Glance"
date: 2012-11-07 21:16
comments: true
categories: javascript computer-science
---

The next version of ECMAScript, or "Harmony", introduces [Proxies](http://wiki.ecmascript.org/doku.php?id=harmony:proxies). What is a proxy, you ask? Merriam-webster defines it as "authority or power to act for another". And that's simply what a Proxy object allows you to do in JavaScript: act on behalf of another object. In other words, a Proxy object can be used to intercept calls, or property access, from some other object. Use cases may not be apparent at first so consider the following: Logging when we get/set a property on an object, fire events when getting/setting a property on an object, transparent data binding on an object. In this post I'll explore the first and the last. 

### Logging Access

Let's imagine you would like to create an audit log of when an object was changed. Maybe you're trying to track down a bug, or maybe you want an audit trail of modifications. One solution is to use <code>myObject.set('prop', 'value');</code> or <code>myObject.get('prop')</code>. This solution is pretty simple! However, it requires boiler-plate code and extra typing. It would be nice if we could use regular js accessor properties right? Or, if we're tracing down a bug we may end up littering our code with <code>console.log</code> statements. We can alleviate these problems by using a Proxy.

{% highlight js %}
var loggable = function(obj, logger) {
	return Proxy.create({
		get: function get(receiver, prop) {
			logger.info('Getting ' + prop);
			return obj[prop];
		},
		set: function set(receiver, prop, value) {
			logger.info('Setting ' + prop + ' to ' + value + '; was ' + obj[prop]);
			debugger;
			obj[prop] = value;
		}
	});
};

var person = { name: 'Alice', age: 22 };
person = loggable(person, { 
	info: function info(str) {
		console.log(str);	
	}
});

document.write(person.name);
person.name = 'Alyson';
document.write(person.name);
{% endhighlight %}

The code above will proxy the get and set mutators to the Proxy object created in <code>loggable</code>. It starts by defining a function that creates a Proxy for some object <code>obj</code> and using some logger <code>logger</code>. Next, we've defined the action required for the object's get and set mutators. In <code>get</code> we're simply logging that a property was accessed. In <code>set</code> we're logging what we're updating the object to as well as what it was in its old state. The <code>debugger;</code> statement is a testament to how easy debugging becomes. We create a person object and then assign it to a Proxy object. Finally, we execute basic get and set mutators to see our logging statements appear in the console.

I've ran this code in Firefox and Chrome (with 'Enable Experimental JavaScript' set to enabled in about://flags).

### Data Binding

Data binding has caught on in the recent explosion of JavaScript frameworks such as [AngularJS](//angularjs.org), [Ember.js](//emberjs.com), [Knockout](//knockoutjs.com), etc. Data binding allows us to update our JavaScript object and instantly see the change reflected on our page. Some frameworks, like Ember.js, make you use the <code>.get()</code> and <code>.set()</code> notation which, as previously stated, is extra typing and doesn't use the native js mutators. On the other hand, frameworks like Angular and Knockout allow you to use native mutators but must constantly monitor the state of every object with some sort of equality check performed at a regular interval.

The Proxy object gives us the best of both worlds. We can use native mutators and we don't need any monitoring. Here's an <em>very</em> naive [example](https://tinker.io/ba98d/19).

{% highlight js %}
function trap(obj, index) {
	var empty = [];
	return Proxy.create({
		get: function (receiver, prop) {
			return obj[prop];
		},
		set: function (receiver, prop, value) {
			obj[prop] = value;
			(index[prop] || empty).forEach(function (binding) {
				binding.firstChild.nodeValue = value;
			});
		}
	});
}

var scope = {
	person: {}
};

window.addEventListener('load', function() {
	parseDom();
	scope.person.name = 'Alyson Wonderland';
	scope.person.age = 21;
	scope.person.city = 'rabbit';
	scope.person.state = 'Hole';
	var count = 0;
	setInterval(function(){
		scope.person.age++;
	}, 1000);
});

function parseDom() {
	var models = document.querySelectorAll('[data-model]');
	[].slice.call(models).forEach(function(element) {
		var name = element.dataset.model,
			model = scope[name];
		if (model) {
			bindElementToObj(element, model, name);	
		}
	});
}

function bindElementToObj(element, obj, name) {
	var index = createBindingsIndex(element);
	scope[name] = trap(obj, index);
}

function createBindingsIndex(element) {
	var bindingsIndex = {},
		nodes = [].slice.call(element.childNodes),
		node = null,
		testRe = /\{\{\w+\}\}/;
	
	while(nodes.length) {
		node = nodes.shift();
		if (node.nodeType !== 3) {
			if (node.childNodes.length === 1 &&
				node.firstChild.nodeType === 3 &&
			    testRe.test(node.firstChild.nodeValue)) {
				addBindingsToIndex(node, bindingsIndex);
			}
			nodes.push.apply(nodes, node.childNodes);
		}
	}
	return bindingsIndex;
};

function addBindingsToIndex(node, index) {
	var fragment = document.createDocumentFragment(),
		template = node.removeChild(node.firstChild).nodeValue,
		split = template.split(/\{\{\w+\}\}/);
	stripBindings(template).forEach(function (match, i) {
		var span = document.createElement('span');
		if (!index[match]) {
			index[match] = [];
		}
		span.appendChild(document.createTextNode(match));
		index[match].push(span);
		fragment.appendChild(document.createTextNode(split[i]));
		fragment.appendChild(span);
	});
	fragment.appendChild(document.createTextNode(split[split.length - 1]));
	node.appendChild(fragment);
};

function stripBindings(text) {
	var matches = [], re = /\{\{(\w+)\}\}/g, match;
	while (match = re.exec(text)) {
		matches.push(match[1]);
	}
	return matches;
};

{% endhighlight %}

If you made it this far, I won't bother you with the details of what's going on but give a high level overview. We look at our html for any tags that have the <code>data-model</code> attribute. Next, we loop over every match and perform a [breadth-first search](http://en.wikipedia.org/wiki/Breadth-first_search) for text nodes that contain our special template syntax of <code>\{\{&lt;nameOfAttributeOnModel&gt;}}</code>. Once we've built an [inverted index](http://en.wikipedia.org/wiki/Inverted_index) of template matchings to nodes that need updating, we create a Proxy of the data model, passing it our index so that anytime a property is changed on our Proxy we can instantly find what nodes need updating to reflect in our DOM. Finally, to show data binding working, we update the age of our test object every second which is reflected in the DOM; no verbose syntax or object equality checking required.

### Conclusion
Proxies help provide many conveniences but comes with its share of problems. For example, your proxy will also trap calls to functions. This means you'll have to run a <code>typeof</code> on your property to see if it's a function. Fortunately the Proxy specification is still very much a working draft. Perhaps it will get a <code>call</code> trap for trapping function invocations. I wouldn't recommend the use of proxies in production code. However, for debugging code I'd highly recommend it.