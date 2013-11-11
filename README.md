# Parallaxify
### add depth to your project

*parallaxify* is a jQuery plugin that adds parallax effects to elements and backgrounds based on gyroscope sensor (device orientation) data or mouse movement. Due to the recent hype started due to the introduction of a parallax effect in iOS 7, I created a plugin that allows anyone to easily incorporate parallax effects to their websites.

## Demos

* [Galaxy](http://hwthorn.github.io/parallaxify/demo/galaxy.html)
* [iOS 7 MockUp](http://hwthorn.github.io/parallaxify/demo/ios.html)

## Download

Get the [minified](https://raw.github.com/hwthorn/parallaxify/master/jquery.parallaxify.min.js) version or the [source](https://raw.github.com/hwthorn/parallaxify/master/jquery.parallaxify.js) here.

## Get things going

In order to add a parallax effect to any element you can run `.parallaxify()` on the element or run it globally on 'window':

``` js
    // Run it on single element
    $('#my-element').parallaxify();
    // or globally
    $.parallaxify();
```

You can also run `.parallaxify()` on the fly, e.g. for hover effects and destroy the instance on blur using the built-in destroy method (Thanks to [matlembo](https://github.com/matlembo) for pointing out the following example - see https://github.com/hwthorn/parallaxify/issues/2 ):

``` js
$('#my-element').hover(
    function(){
        $(this).parallaxify(args);
    },
    function(){
        $(this).parallaxify('destroy');
    }
);
```

The horizontal and vertical distances that each element can or should travel can be defined through attributes as follows:

``` html
    <div data-parallaxify-range-x="100" data-parallaxify-range-y="50"></div>
```

You can also use the shorthand attribute `data-parallaxify-range` to set x and y range with one setting. In a similar way you can set backgrounds to reposition based on the input.

``` html
    <div data-parallaxify-background-range="100"></div>
```

All available attributes:
* `data-parallaxify-range`
* `data-parallaxify-range-x`
* `data-parallaxify-range-y`
* `data-parallaxify-background-range`
* `data-parallaxify-background-range-x`
* `data-parallaxify-background-range-y`


## Fine tuning the plugin

*parallaxify* exposes a variety of options that let you influence how element positioning is achieved, configure filtering of sensor data, configure the movement algorithm, and change general plugin settings.

These are all settings that can be configured as part of the plugin:

``` js
    $.parallaxify({
        // enable parallax effect for horizontal, vertical or both directions
        horizontalParallax: true,
        verticalParallax: true,
        
        // enable or disable parallax effect for elements or backgrounds
        parallaxBackgrounds: true,
        parallaxElements: true,
        
        // set which positioning property is to be used
        // options include 'position' or 'transform' using css transformations
        positionProperty: 'position',
        
        // enable for responsive layouts
        // (upon orientation changes or window resizing element positions are reevaluated
        responsive: false,
        
        // enable or disable mouse or gyroscope data as input for the plugin
        useMouseMove: true,
        useGyroscope: true,
        
        // use a Low Pass Filter to smooth sensor readings (1 = no filter)
        alphaFilter: 0.9,
        
        // set which motion type algorithm is to be used
        // options include 'natural', 'linear', 'gaussian', or 'performance'
        motionType: 'natural',
        mouseMotionType: 'gaussian',
		
		// define which sensor input has priority over the other
		// options are either 'mouse' or 'gyroscope'
		inputPriority: 'mouse',
        
        // define the delta angle (0 < motionAngle < 90) 
        // that is used to render max parallax in this direction
        motionAngleX: 80,
        motionAngleY: 80,
        
        // enable of adjustment of base position (using a Low Pass Filter)
        // (adapting to device usage while plugin is running)
        adjustBasePosition: true,
        // alpha for Low Pass Filter used to adjust average position
        alphaPosition: 0.05,
    });
```

### Positioning

*parallaxify* allows you to control how elements are repositioned. Out of the box you can choose between positioning via `top: 0px; left: 0px` properties or CSS3 transformations. You can also extend this by writing your function.

``` js
    // enable parallaxify with CSS3 transformations for positioning
    $('#element').parallaxify({
        positionProperty: 'transform'
        });
        
    // defining your own positioning function
    $.parallaxify.positionProperty.rotate = {
        setPosition: function($element, left, originalLeft, top, originalTop) {
            $element.css('transform', 'rotateX(' + left + 'deg) rotateY(' + top + 'deg)');
        }
    };
        
    // using your own positioning function
    $('#other').parallaxify({
        positionProperty: 'rotate'
    });
```

### Motion algorithms

In order to control how the input data (i.e. the mouse movement or the gyroscope sensor data) influences the repositioning of the elements and backgrounds you can use one of the pre-defined `motionType` or write your own algorithm. `motionType` and `mouseMotionType` allow you to configure the behavior for gyroscope and mouse input separately.

*   `linear` algorithm
    Nothing more to say. The elements are repositioned linearly with the movement, reaching the maximum displacement at the defined `motionAngleX` and `motionAngleY` maximum angles (and remain at that position beyond these angles);
*   `natural` algorithm
    Natural movment uses a tangent function to calulate the repositioning. This is your number one choice if you want to create an effect similar to the iOS 7 home screen. Remember to set the maximum angles (`motionAngleX` and `motionAngleY`) accordingly. Good choices here are probably `80` for both. Keep in mind that if you set the angles too low, that the movement will stop at these angles.
*   `gaussian` algorithm
    Here we use an approximation to the Gaussian Cumulative Distribution function (short CDF). The displacement is large at small angles (or mouse movements at the screen center) and is almost unnoticeable at large angles (or mouse movements close to the screen border).
*   `performance` algorithm
    An approximation to the tangent function that is intended to speed up calculation in javascript and hence have a slightly better performance

Similarly to the positioning you can write your own function to fit your needs:

``` js
    // enable parallaxify with natural motion (based on sensor data) and the gaussian algorithm for mouse movement
    $('#element').parallaxify({
        motionType: 'natural',
        mouseMotionType: 'gaussian'
        });
        
    // defining your own motion type
    // example for linear motion
    $.parallaxify.motionType.linear = {
        function(delta, deltaMax) {
            if (delta <= -deltaMax) return 1;
            if (delta >= deltaMax) return -1;
            return -delta/deltaMax;
        }
    };

    // using the linear positioning function
    $('#other').parallaxify({
        positionProperty: 'linear'
    });
```

The following graph plots the `linear` (blue), `natural` (red), and `gaussian` (green) alogrithm for `motionAngleX: 85`:
![plot of algorithms](https://raw.github.com/hwthorn/parallaxify/master/doc/plot.png "Plot of algorithms: linear (blue), natural (red), gaussian (green)")

## License

Released under the MIT license.
http://hwthorn.mit-license.org

## Thanks

Having seen Mark Dalgleish' parallax scrolling plugin [stellar.js](http://markdalgleish.com/projects/stellar.js) I got inspired and built parallaxify based on his plugin.
