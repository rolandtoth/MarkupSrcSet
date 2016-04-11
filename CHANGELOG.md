#Changelog


### 0.0.2 (2016-04-11)

- rewrite to use image methods instead of global functions (image->srcset(), image->bgset())
- image sets are stored in global $config->imagesets array (faster lookup)
- $image->srcset() doesn't render full image markup but only required attributes (just like $image->bgset)
- only "class" can be passed as additional attribute
- load scripts only if there's a lazysize element on the page (currently checking only for the required 'lazyload' class name)


### 0.0.1 (2016-04-10)

- initial release