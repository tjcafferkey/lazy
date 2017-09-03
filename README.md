# Lazy Loader Plugin

A base image loading, event driven class that is easily extendable to support any loading criteria.

## How it works

The `LazyLoad` base class takes a CSS selector to identify all images to be lazy loaded. These images need a `data-src` attribute which defines path to the image. The image's `src` attribute can be omitted or left empty.

```html
<img data-src="images/my-lazy-loading-image.png">
```

Upon initialisation, the `LazyLoad` class creates an array of image data, storing each image element, its src and a loaded attribute used to identify if the image has been loaded.

```javascript
// each lazy image stored in the images array is represented with the following data structure
{
  image: <Image Elememt>
  src: <String>
  loaded: <Boolean>
}
```

An event listener is added to each image upon initialisation of a new `LazyLoad` instance, listening for the custom event `lazyload` that's configured internally.

An instance of `LazyLoad` inherits a single method `fireEvent`, which takes an image element as an argument and fires the `lazyload` event on the image. This event triggers the loading of the image into cache and, once loaded, sets the src attribute of the image so it appears in the document.

```javascript
// an instance of LazyLoad looks like this
{
  images: <Array> // stores objects representing each lazy load image (see above for data structure)
  fireEvent: <Function> // inherited
}
```

## Lazy Scroll

The `LazyScroll` class is a wrapper around `LazyLoad`, using positional data to determine if an image is in the viewport. If an image is in the viewport the `lazyload` event is fired on the image to load and display it.

The `LazyScroll` class appends the positional data of each image to the image data stored as part of the base class.

```javascript
// lazy scroll images get an additional imagePosition property
{
  img: <Image Elememt>
  src: <String>
  loaded: <Boolean>
  imagePosition: <Object>
}
```

## Usage

To use the `LazyScroll` class, call the class with a CSS selector and the plugin will do the rest, loading images as they appear in the viewport.

```javascript
const lazy = new LazyScroll('.lazy-image');
```

To extend the base class and add custom loading criteria use the following pattern:

```javascript
class CustomLazy extends LazyLoad {
  constructor(selector) {
    super(selector);
    // modify the images property to add custom data 
    this.images = this.images.map(function(lazyImage) {
      // return the lazyImage object with additional data
    });
  }
}

// use some other criteria to trigger an image load
// internally in the class loop through the array of images, if one meets the criteria to load
// call the fireEvent method passing in the image to load

const lazy = new CustomLazy('.lazy-image');
lazy.images.filter(function(lazyImage) {
  // get the images yet to be loaded
  return !lazyImage.loaded;
}).forEach(function(lazyImage) {
  // test to see if the image meets the loading criteria, if it does fire the event
  lazy.fireEvent(lazyImage.image);
  // and update the image data to show the image has loaded
  lazyImage.loaded = true;
});
```

### TODO:
* A proximity based loader class
* Support background images and picture element
* Handling when images fail to load
* Show loading spinner whilst images load
