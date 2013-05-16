# jQuery Panzoom

Panzoom is a progressive plugin to create panning and zooming functionality for an element. Panzoom supports the same browsers as jQuery 2.0 and can be used with jQuery 1.9.0+ or jQuery 2.0+.
Rather than setting width and height on an image tag, Panzoom uses CSS transforms and matrix functions to take advantage of hardware/GPU acceleration in the browser, which means the element can be _anything_: an image, a video, an iframe, a canvas, text, WHATEVER.
And although IE<=8 is not supported, this plugin is future-proof.

jquery.panzoom.min.js (6.9kb/2.7kb gzip), included in this repo, is compressed with [uglifyjs](https://github.com/mishoo/UglifyJS).

## Mobile support

Panzoom includes support for touch gestures and even supports __pinch gestures__ for zooming. It is perfectly suited for both mobile and desktop browsers. You'll be amazed at how well this performs on your mobile device.

iOS has always been supported. Android is supported as of v0.6.0.

## SVG support

As of v0.2.0, Panzoom supports panning and zooming SVG elements directly, in browsers that support SVG.

## Dependencies

Panzoom obviously depends on jQuery, but it also depends on [Modernizr](http://modernizr.com/) for its touch gesture support detection.<br>
If Modernizr is not included, touch gestures will simply not be used and it will fail silently.<br>
However, mobile behavior is not supported without Modernizr and its touch support feature detect.

## Loading Panzoom
Panzoom can obviously be included with your scripts at the end of the body, but Panzoom supports AMD for javascript module love.

With script tags:

```html
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
<script src="/js/plugins/jquery.panzoom.js"></script>
```

With AMD loader in an anonymous module:

```js
define([ "jquery", "plugins/jquery.panzoom" ], function( $ ) {
  $(document).ready(function() {
    $(".panzoom-elements").panzoom();
  });
});
```

## Initialization

```js
$(".panzoom-elements").panzoom();

// Pass options
$("a.panzoom-elements").panzoom({
  minScale: 0,
  $zoomRange: $("input[type='range']")
});
```

## Options

All options can be overridden by passing an object literal like any other plugin,<br>
or with the `"option"` method.<br>

```js
Panzoom.defaults = {
  // Should always be non-empty
  // Used to bind jQuery events without collisions
  // A guid is not added here as different instantiations/versions of panzoom
  // on the same element is not supported, so don't do it.
  eventNamespace: ".panzoom",

  // Whether or not to transition the scale
  transition: true,

  // Default cursor style for the element
  cursor: "move",

  // There may be some use cases for zooming without panning or vice versa
  disablePan: false,
  disableZoom: false,

  // The increment at which to zoom
  // adds/subtracts to the scale each time zoomIn/Out is called
  increment: 0.3,

  minScale: 0.4,
  maxScale: 5,

  // Animation duration (ms)
  duration: 200,
  // CSS easing used for scale transition
  easing: "ease-in-out",

  // Zoom buttons/links collection (you can also bind these yourself - e.g. `$button.on("click", function( e ) { e.preventDefault(); $elem.panzooom("zoomIn"); });` )
  $zoomIn: $(),
  $zoomOut: $(),
  // Range input on which to bind zooming functionality
  $zoomRange: $(),
  // Reset buttons/links collection on which to bind the reset method
  $reset: $(),
  // For convenience, these options will be bound to panzoom events
  // These can all be bound normally on the panzoom element
  // e.g. `$elem.on("panzoomend", function( e, panzoom ) { console.log( panzoom.getMatrix() ); });`
  onStart: undefined,
  onChange: undefined,
  onEnd: undefined
};
```

## Methods

Methods can be called in the same way as a widget from the jQuery UI widget factory. Pass a method name when calling panzoom. Strings are interpreted as method names.

### `option()`

```js
// One at a time
// Sets the scale increment option
$elem.panzoom("option", "increment", 0.4);

// Several options at once
$elem.panzoom("option", {
  increment: 0.4,
  minScale: 0.1,
  maxScale: 2,
  duration: 500,
  $reset: $("a.reset-panzoom, button.reset-panzoom")
});
```

Any option can be changed. See the defaults above for a list.

### `reset( [animate] )`

__Arguments__

  1. `animate` _{Boolean}_: Whether to animate the reset (default: true)

```js
$elem.panzoom("reset");
```

Reset the transform matrix to its original value. All panning and zooming is reset.

### `resetZoom( [animate] )`

__Arguments__

  1. `animate` _{Boolean}_: Whether to animate the reset (default: true)

```js
$elem.panzoom("resetZoom");
$elem.panzoom("resetZoom", false);
```

Reset the scale to its original value.

### `resetPan( [animate] )`

__Arguments__

  1. `animate` _{Boolean}_: Whether to animate the reset (default: true)

```js
$elem.panzoom("resetPan");
$elem.panzoom("resetPan", false);
```

Reset the pan to its original value.

### `zoom( [scale[, opts]] )`

__Arguments__

  1. `scale` _{Number|Boolean}_: The exact scale to which to zoom or a boolean indicating to transition a zoom out
  2. `opts` _{Object}_:
    a. `opts.noSetRange` _{Boolean}_: Specify that the method should not set the $zoomRange value (as is the case when $zoomRange is calling zoom on change)
    b. `opts.middle` _{Object}_: Specify a middle point towards which to gravitate when zooming
    c. `opts.silent` _{Boolean}_: Silence the zoom event

```js
// Transition a zoom in based on the scale increment, min and max values
$elem.panzoom("zoom");

// Transition a zoom out
$elem.panzoom("zoom", true);

// Set the scale immediately without a transition
// and silence the zoom event
$elem.panzoom("zoom", 1.2, { silent: true });
```

Transition a zoom in based on the scale increment, min and max values, and animation duration and easing. This method handles both zooming in and zooming out.<br>
If the method is passed a number, `zoom()` will immediately set that scale without transitioning. This is mostly useful for the range input and pinch gestures.

### `instance()`

```js
var panzoom = $elem.panzoom("instance");
```

Retrieves the Panzoom instance(s) from the set. If there are multiple elements in the set, you will get an array of instances. If there is only one, you will just get that instance of Panzoom.

### `destroy()`

```js
$elem.panzoom("destroy");
```

Unbinds all events and removes all data, including the Panzoom instance on the element.

## Internal

These methods are _basically_ private, but could be useful under certain circumstances.

### `isPanning()`

Returns a boolean indicating whether the element is currently panning.

### `getMatrix( [transform] )`

Retrieve an array of values for the specified transform or for the current transform on the panzoom element.

```js
$elem.panzoom("getMatrix");
// => [1, 0, 0, 1, 0, 0]
```

### `setMatrix( matrix[, options] )`

__Arguments__

  1. `matrix` _{Array}_: Matrix to set
  2. `options` _{Object}_
  3. `options.animate` _{Boolean}_: If true, a transitionw will be set to animate the transform change
  4. `options.silent` _{Boolean}_: If true, the change event will not be triggered

```js
// Flip the element upside down
$elem.panzoom("setMatrix", [ 1, 0, 0, -1, 0, 0 ]);
```

Sets the transform matrix of the panzoom element. It accepts the matrix as an array. The return value is `undefined`.

### `transition( [off] )`

```js
$elem.panzoom("transition");
// Turn off transition
$elem.panzoom("transition", true);
// Note: this is different than...
$elem.panzoom("option", "transition", true);
// ... which just sets the `transition` option, indicating whether transitioning is allowed at all.
// If the transition option is false, `$elem.panzoom("transition")` will only ever set transition to "none".
```

Applies the transition to the element. If `off` is true, it removes the transition.


## Events

### `"panzoomstart"`

__Arguments Received__

  1. `e` _(jQuery.Event)_: jQuery event object
  2. `panzoom` _(Panzoom)_: The panzoom instance
  3. `startPageX` _(Number|TouchList)_: The pageX on the mousedown event or the touches list
  4. `startPageY` _(Number)_: The pageY on the mousedown event

Fired when the user starts a move or pinch zoom gesture on mobile, this event receives the same arguments as the private `_startMove()` method.

If startPageY is undefined, you know the panzoom was triggered by a touch event and not a mousedown event.

### `"panzoomchange"`

__Arguments Received__

  1. `e` _(jQuery.Event)_: jQuery event object
  2. `panzoom` _(Panzoom)_: The panzoom instance
  3. `transform` _(String)_: The exact transform set during the change

Fired whenever the matrix is changed by `setMatrix()` (whether internally or externally).

_Try not to put to much in this event as it could slow down dragging._

__Note__: This event can be silenced when setMatrix is called directly.

### `"panzoomzoom"`

__Arguments Received__

  1. `e` _(jQuery.Event)_: jQuery event object
  2. `panzoom` _(Panzoom)_: The panzoom instance
  3. `scale` _(Number)_: The zoom scale set by the plugin

Fired whenever the zoom is changed by this plugin.

__Note__: This event can be silenced when zoom is called directly.

### `"panzoompan"`

__Arguments Received__

  1. `e` _(jQuery.Event)_: jQuery event object
  2. `panzoom` _(Panzoom)_: The panzoom instance
  3. `x` _(Number)_: Translate x value
  4. `y` _(Number)_: Translate y value

Fired whenever the pan is changed by this plugin.

_Try not to put to much in this event as it could slow down dragging._

### `"panzoomend"`

__Arguments Received__

  1. `e` _(jQuery.Event)_: jQuery event object
  2. `panzoom` _(Panzoom)_: The panzoom instance
  3. `changed` _(Boolean)_: Whether the matrix changed during the panzoom event.

This event is fired when the user finishes a move or finishes a pinch zoom gesture on mobile.

## Testing

Tests can be run by opening [test/index.html](http://timmywil.github.com/jquery.panzoom/test/) in a browser or by using [`grunt`](http://gruntjs.com/) and [phantomjs](http://phantomjs.org/). See [CONTRIBUTING.md](https://github.com/timmywil/jquery.panzoom/blob/master/CONTRIBUTING.md) for more info.

Tests are written with [mocha](http://visionmedia.github.com/mocha/) and [chai for bdd-style assertions](http://chaijs.com/api/bdd/).
