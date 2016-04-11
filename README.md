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
$image->srcset($imageSetName [, $classes, $resizeOptions])
$image->bgset($imageSetName [, $classes, $resizeOptions])
```

*Srcset example:*

```php
<img <?php echo $page->featured_image->srcset('featured-image'); ?>>
```

This will generate the required srcset attributes using the image set "featured image" set in module settings (see below). It also adds the CSS class "lazyload" which is required by the lazysizes JavaScript to work.

*Srcset example with additional classes and image quality:*

```php
<img <?php echo $page->featured_image->srcset('featured-image', 'my-class inline-block', array('quality' => 80)); ?>>
```

This will add clases 'my-class' and 'inline-block' to the image, and the third parameter will set the image quality used for resize. For the latter see ProcessWire's image resize options [here](https://processwire.com/api/fieldtypes/images/).

Note that the "lazyload" class will be always added even if setting other classes.

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