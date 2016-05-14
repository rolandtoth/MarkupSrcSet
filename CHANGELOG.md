#Changelog

### 0.0.4 (2016-05-14)

- new module option to use "srcset" attribute instead "data-srcset" (requested by benbyford)
- new module option to disable adding "lazyload" class automatically
- "lazyload" class can be disabled by passing empty string or false as class name


### 0.0.3 (2016-05-12)

- do not automatically add "lazyload" class if class was provided as a parameter
- flags can be passed as 4th parameter (currently only 'noFallbackImage' is available)
- added attrchange lazysize plugin
- better checking of whether the site has any srcset or bgset elements



### 0.0.2 (2016-04-11)

- rewrite to use image methods instead of global functions (image->srcset(), image->bgset())
- image sets are stored in global $config->imagesets array (faster lookup)
- $image->srcset() doesn't render full image markup but only required attributes (just like $image->bgset)
- only "class" can be passed as additional attribute
- load scripts only if there's a lazysize element on the page (currently checking only for the required 'lazyload' class name)



### 0.0.1 (2016-04-10)

- initial release
