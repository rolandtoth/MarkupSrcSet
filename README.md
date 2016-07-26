MarkupSrcSet for ProcessWire
================

Generate srcset and bgset markup for [lazysizes](https://github.com/aFarkas/lazysizes) in [ProcessWire CMS](http://processwire.com/).

## Features

- generate srcset/bgset markup for [lazysizes](https://github.com/aFarkas/lazysizes)
- set image sizes in JSON array
- add required JavaScripts automatically (optional)
- fallback to smallest image size if JavaScript is not available

## Usage

1. Install the module

1. Configure breakpoints and image size sets in module settings

1. Use methods $image->srcset() or $image->bgset() in template files to generate markup

### Syntax

```php
$image->srcset($imageSetName [, $classes, $resizeOptions, $flags])
$image->bgset($imageSetName [, $classes, $resizeOptions, $flags])
```

*Srcset example:*

```php
<img <?php echo $page->featured_image->srcset('featured-image'); ?>>
```

This will generate the required srcset attributes using the image set "featured image" set in module settings (see below). It also adds the CSS class "lazyload" which is required by the lazysizes JavaScript to work.

*Srcset example with additional classes and image quality:*

```php
<img <?php echo $page->featured_image->srcset('featured-image', 'lazyload my-class inline-block', array('quality' => 80)); ?>>
```

This will add clases 'lazyload', 'my-class' and 'inline-block' to the image, and the third parameter will set the image quality used for resize. For the latter see ProcessWire's image resize options [here](https://processwire.com/api/fieldtypes/images/).

You can disable automatically adding "lazyload" class in the module settings. However, you can still force to add the class in your templates manually.

Passing `false` or `""` (empty string) as a class name will add no class to the markup.

*Bgset example:*

```php
<div <?php echo $page->images->first()->bgset('hero'); ?>>Lorem ipsum</div>
```

This will generate the following attributes:

- "data-bgset" with urls to different image size variations
- "lazyload" class
- "data-sizes"
- inline style for the mobile background image (fallback if JavaScript is not available)

Custom classes and image resize options work the same way as in $image->srcset().

#### Flags (from v0.0.3)

Flags can modify markup. These are simple strings separated with comma or space.

Currently there is only one flag available: 'noFallbackImage'.

**noFallbackImage**

```php
<img <?php echo $page->featured_image->srcset('featured-image', null, null, 'noFallbackImage'); ?>>
```

This will tell the module to add no fallback image so the generated markup won't have "src" attribute.


## Settings

### Breakpoints

Space-separated list of responsive breakpoints (mobile-first).

*Example:*

```
360w 720w 990w 1220w
```

### Image Sets

JSON array that holds image sizes.

There are three ways to specify image sets, see the exampes below.

Common rules:
- the first size is always the base (mobile) image size, specified in an array containing width and height
- the rest of the sizes can be specified in either width/height arrays or multipliers which multiply the base size
- setting zero in width or height means 'auto' (native ProcessWire feature)
- setting null for width or height means 'calculate this value from the mobile value'
- if a breakpoint needs to be skipped, use null for the image size

**Examples**

*Setting the mobile image size + set width or height for the rest of the breakpoints. Keeps image ratio.*

```
{
    "hero": [
        [640, 210], [1080, null], [1920, null]
    ]
}
```

By setting null as the height, image height will be calculated from the mobile size using the current width. For example, the second height will be `210/640 * 1920 = 630 pixels`.


*Setting the mobile image size + set multipliers for the rest of the breakpoints. Also keeps image ratio.*

```
{
    "featured-image": [
        [360, 240], 1.333, 2.667
    ]
}
```

When using multipliers instead of size arrays, the image width and height will be calculated from the mobile size. In the example above the calculated sizes will be `[360, 240], [480, 320], [960, 640]`.

*Setting all image sizes manually. This allows setting arbitrary image ratios.*

```
{
    "gallery-thumb": [
        [240, 120], [480, 300], [800, 576]
    ]
}
```

*All these can be combined:*


```
{
    "hero": [
        [640, 210], [1080, null], [1920, null]
    ],
    "featured-image": [
        [360, 240], 1.333, 2.667
    ],
    "gallery-thumb": [
        [240, 120], [480, 300], [800, 576]
    ]
}
```

*Skipping an image size:*

```
{
    "only-mobile-and-desktop": [
        [300, 120], null, [1280, 720]
    ]
}
```

This will ignore the "tablet" size so the mobile image size will be used below the desktop breakpoint.


### Add "lazyload" class

Here you can disable adding "lazyload" class required by the lazysizes JavaScript globally. This can be handy if you would like to use the native "srcset" tag instead of the default "data-srcset" (see below).

However, passing "lazyload" to the $img->srcset() or $img->bgset() will still add the class to the markup:

```php
	<img <?php echo $page->featured_image->srcset('featured-image', 'lazyload'); ?>>
```

### Load scripts

The module loads the following JavaScript files that you can entirely disable if you check this.

- lazysizes.min.js
- ls.bgset.min.js
- ls.attrchange.min.js
- picturefill.min.js

### Use "srcset" attribute

Check to add "srcset" tag on images instead of "data-srcset". This allows native srcset functionality in supported browsers.

Note that you need to make sure the "lazyload" class is not added to the markup because the lazysizes JavaScript will block loading these images.

Also note that "data-bgset" will not become "bgset" because there is no native support for that. If you use bgset in your code, make sure you load the required JavaScript files to make them work.