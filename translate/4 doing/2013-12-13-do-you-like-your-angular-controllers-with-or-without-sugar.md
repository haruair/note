---
layout: post
title: Do You Like Your Angular Controllers with or without Sugar?
date: 2013-12-13 19:09
author: John
comments: true
categories: [angular, patterns, Uncategorized]
original: http://www.johnpapa.net/do-you-like-your-angular-controllers-with-or-without-sugar/
---
<p><img src="http://www.johnpapa.net/wp-content/uploads/2013/12/sugar-make-us-age-1.jpg" alt="sugar-make-us-age-1" width="150" height="150" class="alignleft size-full wp-image-22911" />Even if you've only read about <a href="http://angularjs.org" target="_blank">Angular</a>, the odds are you've seen the rampant use of <code>$scope</code> in the C of MVC (controllers). <code>$scope</code> is the glue between the Controller and the View that helps with all of our data binding needs. Recently the Angular team opened up a new way to use <code>$scope</code> with Controllers. So now you can use <code>$scope</code> (what I'll refer to as Classic Controllers) and you can use this (what the Angular team and I refer to as Controller As). I hear a lot of questions about these 2 techniques. Everyone loves choice, but at the same time, most folks like to know clearly what they are getting or giving up with the choices. So let's discuss the two controller constructs in Angular (with <code>$scope</code> and Controller As) and how scope plays in both of these.</p>

<blockquote>
  <p>Both Classic Controller and Controller As have <code>$scope</code>.  That's super important to understand. You are not giving up any goodness with either approach. Really. Both have their uses.</p>
</blockquote>

<h3>First, some history ...</h3>

<p><code>$scope</code> is the "classic" technique while "controller as" is much more recent (as of version 1.2.0 officially though it id appear in unstable pre-releases prior to this). Both work perfectly well and the best guidance I can give is to try to be consistent with choosing one or the other. You can mix them in the same app, but for Pete's sake have an explicit reason for it first. So pick one and roll with it. The most important thing is to be consistent. Which one? That depends on you. There are many more examples out there of <code>$scope</code>, but "controller as" is picking up steam as well. Is one better than the other? That's debatable. So how do you choose?</p>

<h3>Comfort I prefer the "controller as" because I like hiding the</h3>

<p><code>$scope</code> and exposing the members from the controller to the view via an intermediary object. By setting <code>this.*</code>, I can expose just what I want to expose from the controller to the view. You can do that with <code>$scope</code> too, I just prefer to use standard JavaScript for this. Overall, for me it really just comes down to personal preference and mine is that I prefer the Controller As technique. In fact, I code it like this:</p>

<pre class="prettyprint linenums">var vm = this;

vm.title = 'some title';
vm.saveData = function(){ ... } ;

</pre>

<p>This feels cleaner to me and makes it easy to see what is being exposed to the view. Notice I name the variable "vm" , which stands for viewmodel. That's just my convention. With $scope I can do the same things, so I'm not adding or detracting with the technique.</p>

<pre class="prettyprint linenums">$scope.title = 'some title';
$scope.saveData = function() { ... };
</pre>

<p>So its up to you there.</p>

<h3>Injection With</h3>

<p><code>$scope</code> I do need to inject <code>$scope</code> into the controller. I don't have to do this with controller as, unless I need it for some other reason (like $broadcast or watches, though I try to avoid watches in the controller). This is another reason I prefer Controller As: I like knowing that I only inject <code>$scope</code> explicitly if I need something besides data binding. Listening for a broadcast message is one example. A watch is yet another, though I try to avoid watches in controllers.</p>

<h3>Trends There appears to, at this time, be more code examples out there using the classic approach with $scope explicitly. However I am seeing more and more examples of Controller As. If you want a file template for creating controllers you can use</h3>

<p><a href="http://sidewaffle.com/" target="_blank">SideWaffle</a>, a plug-in for Visual Studio. It offers both flavors of controllers in its file templates. Don't like sugar? choose classic controllers with $scope. Want some sugar? Choose controller as. The Angular team has given us options and I'm glad they have. I personally prefer the Controller As technique. Either way you get data binding. With Controller As you get some sugar on top that makes working with $scope feel better, in my opinion. So you just have to choose if you want your Angular with or without sugar :)</p>

