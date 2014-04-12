angular-scroll
==============

Only dependent on AngularJS (no jQuery). 6K minified or 1.1K gzipped.

Example
-------
Check out [the live demo](http://durated.github.io/angular-scroll/) or the [source code](https://github.com/durated/angular-scroll/blob/master/example/index.html).

Install
-------
With bower:

    $ bower install angular-scroll

Or download the [production version](https://raw.github.com/durated/angular-scroll/master/angular-scroll.min.js) or the [development version](https://raw.github.com/durated/angular-scroll/master/angular-scroll.js). 

Don't forget to add `duScroll` to your module dependencies. 

`angular.element` Scroll API
----------------------------

This module extends the `angular.element` object with a few jQuery like functions. Note that `$document` is an `angular.element`, for usage example see below.

#### `.scrollTo( left, top [, duration [, easing ] ] )`
Scrolls the element/window to the specified left/top position. If `duration` is specified the scrolling is animated for n milliseconds. If `easing` is ommited the animation will default to the `duScrollEasing` function.

#### `.scrollTo( element [, duration [, easing ] ] )`
Alias of `.scrollToElement`.

#### `.scrollToElement( element [, offset, [, duration [, easing ] ] ] )`
Scrolls to the specified element, if `offset` is passed it will be subtracted from the elements position which is good if one uses floating menus. 

#### `.scrollTop|scrollLeft( )`
Returns current scroll position. 

#### `.scrollTop|scrollLeft( top [, duration [, easing ] ] )` 
Scrolls to specified position in either axis, with optional animation. 


Directives
----------

### ’du-smooth-scroll’
Provides smooth anchor scrolling. 
```html
<a href="#anchor" du-smooth-scroll>Scroll it!</a>
```

### ’du-scrollspy’
Observes wether the target element is in the viewport and adds an `active` class if so. Takes optional `offset` and `duration` attributes which is passed on to `.scrollTo`,

```html
<a href="#anchor" du-scrollspy>Am i active?</a>
```

or together with Bootstrap

```html
<ul class="nav navbar-nav">
  <li du-scrollspy="anchor"><a href="#anchor">Link</a></li>
</ul>
```

### `du-spy-context`
Enables multiple sets of spies on the same target element. Takes optional `offset` attribute to 

```html
<ul du-spy-context class="nav navbar-nav">
  <li du-scrollspy="anchor"><a href="#anchor">Link</a></li>
</ul>
<ul du-spy-context class="nav navbar-nav">
  <li du-scrollspy="anchor"><a href="#anchor">Link</a></li>
</ul>
```
### `du-scroll-container`
Modifies behavior of `du-scrollspy` and ’du-smooth-scroll’ to observe/scroll within and element instead of the window/document. Good for modals/elements with `overflow: auto/scroll`.

```html
<div du-scroll-container>
  <p id="top">This is the top</p>
  <p id="anchor">Scroll to me, or <a href="#top" du-smooth-scroll>the top</a></p>
</div>
```

If your links lie outside of the scrollable element, wrap them with the `du-scroll-container` directive and send the element id as argument:

```html
<ul du-scroll-container="scroll-container">
  <li><a href="#anchor" du-smooth-scroll>Link</a></li>
</ul>
<div id="scroll-container">
  [...]
</div>
```

### [All in together now](http://www.youtube.com/watch?v=cx4KtTezEFg&feature=kp)
The directives play well together, try [the demo](http://durated.github.io/angular-scroll/context.html) or inspect its [source code](https://github.com/durated/angular-scroll/blob/master/example/context.html).

```html
<ul du-spy-context du-scroll-container="scroll-container">
  <li><a href="#anchor" offset="30" du-smooth-scroll du-scrollspy>Link</a></li>
</ul>
<ul du-spy-context du-scroll-container="scroll-container">
  <li><a href="#anchor" offset="30" du-smooth-scroll du-scrollspy>Link</a></li>
</ul>
<div id="scroll-container">
  [...]
</div>
```

Example API Usage
-----------------

### Scroll position
```js
angular.module('myApp', ['duScroll']).
  controller('MyCtrl', function($scope, $document){
    $document.on('scroll', function() {
      console.log('Document scrolled to ', $document.scrollLeft(), $document.scrollTop());
    });
    var container = angular.element(document.getElementById('container'));
    container.on('scroll', function() {
      console.log('Container scrolled to ', container.scrollLeft(), container.scrollTop());
    });
  }
);
```

### Manual smooth scrollTo
```js
angular.module('myApp', ['duScroll']).
  controller('myCtrl', function($document) {
    var top = 400;
    var duration = 2000; //milliseconds

    //Scroll to the exact position
    $document.scrollTop(top, duration);

    var offset = 30; //pixels; adjust for floating menu, context etc
    //Scroll to #some-id with 30 px "padding"
    //Note: Use this in a directive, not with document.getElementById 
    var someElement = angular.element(document.getElementById('some-id'));
    $document.scrollToElement(someElement, offset, duration);
  }
);
```

Configuration
-------------

### Scroll speed
Duration is defined in milliseconds.

To set a scroll duration on a single anchor:
```html
<a href="#anchor" du-smooth-scroll duration="5000">Scroll it!</a>
```

To change the default duration:
```js
angular.module('myApp', ['duScroll']).value('duScrollDuration', 5000);
```

### Scroll easing
Set the `duScrollEasing` value to a function that takes and returns a value between 0 to 1. Here's [a few examples](https://gist.github.com/gre/1650294) to choose from.

```js
function invertedEasingFunction(x) {
  return 1-x;
}
angular.module('myApp', ['duScroll']).value('duScrollEasing', invertedEasingFunction);
```

You can also pass a custom easing function as the fourth argument in `scrollTo`.

Events
------

The `duScrollspy` directive fires the global events `duScrollspy:becameActive` and `duScrollspy:becameInactive` with an angular.element wrapped element as first argument. This is nice to have if you want the URL bar to reflect where on the page the visitor are, like this: 

```js
angular.module('myApp', ['duScroll']).
  config(function($locationProvider) {
    $locationProvider.html5Mode(true);
  }).
  run(function($rootScope, $location){
    $rootScope.$on('duScrollspy:becameActive', function($event, $element){
      //Automaticly update location
      var hash = $element.prop('hash');
      if(hash) {
        $location.hash(hash.substr(1)).replace();
        $rootScope.$apply();
      }
    });
  });
```


Building
--------

    $ grunt
