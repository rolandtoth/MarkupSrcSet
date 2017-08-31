# MarkupSrcSet for ProcessWire

Generate srcset markup for [Lazysizes](https://github.com/aFarkas/Lazysizes) in [ProcessWire CMS](http://processwire.com/).

- [ProcessWire forum topic](https://processwire.com/talk/topic/12981-markupsrcset/)
- [ProcessWire Modules Directory](http://modules.processwire.com/modules/markup-src-set/)


## Installation and usage

1. Install the module

1. Use method $image->srcset($sets[, $options]) in template files to generate markup

**Note**: the module got a major rewrite in version 0.1. Syntax and usage instructions have been completely changed, please read the docs carefully.


### Syntax

```php
$image->srcset($sets [, $options])
```

- **$sets**: string of comma-separated image sizes, optionally with a multiplier or divisor (see the section on Sets below)
- **$options**: ProcessWire image resize option array (optional)


### Parameters


#### Sets

Sets are comma-separated list of the desired image variation dimensions. For convenience it is possible to use a number as a multiplier/divisor to calculate new sets.

The first set is the base size and needs to be in a **width** x **height** format (eg. `640x210`). This doesn't mean it has to be the smallest image set as sets will be automatically sorted in ascending order in the final output.

The rest of the sets can be specified in either the same format or using multipliers/divisors, eg. `*2` or `/3`. This way you can easily add new sizes without calculating the exact dimensions.

There is no limit on the number of sets.

*Examples:*

`1200x900, 600x450, 300x225`

`1200x900, /2, /3` => 400x300, 600x450, 1200x900

`800x400, /2, *2` => 400x200, 800x400, 1600x800

`800x0, /2, *2` => 400x200, 800x400, 1600x800 (if original image ratio was 2:1)

*Tips:*

- set the base set to the largest image size you need and use divisors for smaller ones (it's easier to understand)
- use `0` for width or height and the other dimension will be calculated by ProcessWire from the image ratio


#### Options

Array of ProcessWire's [image resize options](https://processwire.com/api/fieldtypes/images/).

*Example: setting image quality to 80:*

```php
<img src="..." data-srcset="<?php echo $page->featured_image->srcset('200x300,*2,*3', array('quality' => 80)) ?>" data-sizes="auto" alt="" class="lazyload" />
```


## Examples


```php
<?php $img = $page->featured_image; ?>

<img src="..." data-srcset="<?php echo $img->srcset('200x300,500x750,*3') ?>" data-sizes="auto" alt="" class="lazyload" />

<img src="..." data-bgset="<?php echo $img->srcset('1200x900,/2,/3') ?>" data-sizes="auto" alt="" class="lazyload" />

<img src="..." data-srcset="<?php echo $img->srcset('200x300,*2,*3') ?>" data-sizes="auto" alt="" class="lazyload" />
```

*Example output:*

```html
<img src="..." data-srcset="/site/assets/files/1/photo.250x200.jpg 250w,/site/assets/files/1/photo.500x400.jpg 500w,/site/assets/files/1/photo.800x640.jpg 800w" data-sizes="auto" alt="" class="lazyload" />
```

The "lazyload" class and the "data-sizes" attribute need to be added to the markup. The latter can be set to "auto" unless you need specific needs (consult the Lazysizes documentation to learn more).

**Important**: if you are using `data-sizes="auto"` it is recommended to add this into your CSS (from [here](https://github.com/aFarkas/lazysizes#markup-api)):

```css
img[data-sizes="auto"] {
    display: block;
    width: 100%;
}
```

Use `data-srcset` or `data-bgset` tag if you would like to use Lazysizes, otherwise use `srcset`.


## Helpers

The module comes with some helpers to support [markup patterns](https://github.com/aFarkas/lazysizes#recommendedpossible-markup-patterns).

### $img->srcsetUrls

*Access individual image set urls*

After calling $img->srcset(...) the $img object will get a temporary `srcsetUrls` property. This is an array of image sets to retrieve the first, last or any other image set's url:

```php
$img->srcsetUrls['first']
$img->srcsetUrls[0]
$img->srcsetUrls[1]
$img->srcsetUrls[2]
$img->srcsetUrls['last']
```

This can come handy when setting the fallback src attribute:

```php
<?php $img = $page->featured_image; ?>
<img data-srcset="<?php echo $img->srcset('200x300,*2,*3'); ?>" src="<?php echo $img->srcsetUrls['first']; ?>" data-sizes="auto" alt="" class="lazyload" />
```

...or setting bgset fallback image with inline style:

```php
<?php $img = $page->featured_image; ?>
<div data-bgset="<?php echo $img->srcset('200x300,*2,*3'); ?>" style="background-image: url('<?php echo $img->srcsetUrls[0]; ?>');" data-sizes="auto" class="lazyload"></div>
```

...or adding fallback image in a noscript tag:

```php
<?php $img = $page->featured_image; ?>
<img data-srcset="<?php echo $img->srcset('200x300,*2,*3'); ?>" data-sizes="auto" alt="" class="lazyload" />
<noscript>
    <img src="<?php echo $img->srcsetUrls['last']; ?>" />
</noscript>
```

***Remember that $img->srcsetUrls is available only after the srcset() method was called!***


### $config->srcsetFallbackDataUri

*Global variable for `data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==`*

Use this if you prefer a transparent image src as a fallback. You can overwrite it in your code if you wish.

```php
<?php $img = $page->featured_image; ?>
<img data-srcset="<?php echo $img->srcset(); ?>" src="<?php echo $config->srcsetFallbackDataUri; ?>" class="lazyload" data-sizes="auto" alt="" />
```

## Module settings


### Loading scripts

The module loads the 'markupsrcset.js' file that consists of the following files (combined and minified):

- ls.respimg.js
- ls.bgset.js
- ls.attrchange.js
- lazysizes.js

The script is loaded only if the page markup contains "data-srcset" or "data-bgset".


You can disable loading the script at the module's settings page (eg. if you would like to load it manually).