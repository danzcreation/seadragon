# Seadragon


[![Build Status](https://travis-ci.org/eddiej/seadragon.png?branch=master)](https://travis-ci.org/eddiej/seadragon) 
[![Coverage Status](https://coveralls.io/repos/eddiej/seadragon/badge.png)](https://coveralls.io/r/eddiej/seadragon)


[OpenSeadragon](https://openseadragon.github.io/) is an open-source, web-based viewer for displaying high-resolution zoomable images. A viewer is displayed by passing paths to a set of image tiles and a DZI descriptor file to a function provided by the OpenSeadragon Javascript library.
 
OpenSeadragon doesn't handle the creation of the required tile images or descriptor file; this gem provides methods for creating tiles and descriptor files along with a Rails helper method for rendering image viewers in your Rails view templates.  
      
There are a number of Ruby Gems that provide OpenSeadragon functionality. The [Openseadragon](https://github.com/IIIF/openseadragon-rails) gem packages the OpenSeadragon Javascript assets and provides helpers for using them, but leaves the descriptor and tile generation to the end-user. [SeadragonPaperclip](https://github.com/dustmoo/seadragon-paperclip) is a processor for the [Paperclip](https://github.com/thoughtbot/paperclip) gem that generates tiles, but it is tied to Paperclip and doesn't generate descriptor files or provide helper methods. This gem provides everything required for generating and displaying Deep Zoom Images on a webpage.


## Requirements


Seadragon uses [Rmagick](https://github.com/rmagick/rmagick) to process source images - this requires [ImageMagick](http://www.imagemagick.org/script/index.php) to be installed on your system.

## Installation

Install the latest stable release:

```
$ gem install seadragon
```

In Rails, add it to your Gemfile:

```ruby
gem 'seadragon'
```

Restart your server if required to apply the changes.

## Usage


### Backend - Creating the Image Tiles and Descriptor

Before a viewer can be displayed, the required tile images and descriptor file must be created.

First, create a Seadragon::Slice instance, passing to it the path to the image you want to tile, the output directory and a 
unique handle, e.g.

```ruby
slicer = Seadragon::Slicer.new({
  source_path: '/images/starrynight.jpg', 
  tiles_path: '/www/public_html/deep_zoom_images', 
  handle: 'starrynight'
})
```

To generate the image tiles, run:

```ruby
slicer.slice!
```

The descriptor file can the be generated by calling the `write_dzi_specification` method:

```ruby
slicer.write_dzi_specification
```

The steps above would create a `starrynight_files` directory in `/www/public_html/deep_zoom_images` with a subfolder of tile images for each zoom level (calculated according to the dimensions of the image). The descriptor file, configuration information in JSON format required by OpenSeadragon, would be saved to `/www/public_html/deep_zoom_images/starrynight.dzi`. 

### Frontend - Displaying a Viewer

To display a viewer in your view, first ensure the gem's Javascript is referenced from `application.js` in your app:

```css
//= require seadragon
```

Then create an empty element to hold the viewer (giving it a height so that it is displayed), and call the `seadragon` helper method, passing the element's id and the path to the descriptor file, returned by the `write_dzi_specification` method above.

```html
<div id="starrynight_viewer" style="height: 400px;></div>

<%= seadragon({id: "starrynight_viewer", tileSources: "/www/public_html/deep_zoom_images/starrynight.dzi"}) %>
```

This will construct a Javascript call to the bundled OpenSeadragon library which will render a viewer in the element specified.

Any parameters that are used by the OpenSeadragon library can be passed to the `seadragon` helper method. 
If you wanted to use your own set of navigation icons instead of the defaults, and set the defauly zoom level at 5 for example, you would call the `seadragon` method like this:


```html
<%= seadragon({
  id: "starrynight_viewer", 
  tileSources: "/www/public_html/deep_zoom_images/starrynight.dzi",
  defaultZoomLevel: 5,
  prefixUrl: /www/public_html/icons/
}) %>
```

A full list of the OpenSeadragon options is available in the [OpenSeadragon documentation](https://openseadragon.github.io/docs/OpenSeadragon.html#Options). 

## Tests

Tests are written using RSpec. To start the test suite, run:

```ruby
bundle exec rspec
```

or view the tests and results output on [Travis CI](https://travis-ci.org/eddiej/seadragon).






