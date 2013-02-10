GraphicsMagick
==============

A simple Lua wrapper to [GraphicsMagick](http://www.graphicsmagick.org).

gm.convert
----------

This is just a binding to the command line convert utility (images are not loaded
into Lua's memory). Examples:

```lua
gm = require 'graphicsmagick'
gm.convert{
   input = '/path/to/image.png',
   output = '/path/to/image.jpg',
   size = '128x128',
   quality = 95,
   verbose = true
}
```

gm.Image
--------

This is a full C interface to GraphicsMagick's Wand API. We expose one Class: the
Image class, which allows loading and saving images, transforming them, and
importing/exporting them from/to torch Tensors.

Load library:

```lua
gm = require 'graphicsmagick'
```

Create an image, from a file:

```lua
image = gm.Image('/path/to/image.png')
-- or
image = gm.Image()
image:load('/path/to/image.png')
```

Save an image to disk:

```lua
image:save('filename.ext')

-- where:
-- ext must be a know image format (jpg, JPEG, PNG, ...)
-- (GraphicsMagick supports tons of them)
```

Create an image, from a Tensor:

```lua
image = gm.Image(tensor,colorSpace,dimensions)
-- or
image = gm.Image()
image:load(tensor,colorSpace,dimensions)

-- where:
-- colorSpace is: a string made of these characters: R,G,B,A,C,Y,M,K,I
--                (for example: 'RGB', 'RGBA', 'I', or 'BGRA', ...)
--                R: red, G: green, ... I: intensity
--
-- dimensions is: a string made of these characters: D,H,W
--                (for example: 'DHW' or 'HWD')
--                D: depth, H: height, W: width
```

Export an image to a Tensor:

```lua
image = gm.Image('path.jpg')
image:toTensor(type,colorSpace,dimensions)

-- where:
-- type : 'float', 'double', or 'byte'
-- colorSpace : same as above
-- dimensions : same as above
```

In this library, we use a single function to read/write parameters
(instead of the more classical get/set). 

Here's an example of a resize:

```lua
-- get dimensions:
width,height = image:size()

-- resize:
image:size(512,384)

-- resize by only imposing the largest dimension:
image:size(512)
```

Some basic transformations:

```lua
-- flip or flop an image:
image:flip()
image:flop()
```

Show an image (this makes use of Tensors, and Torch's Qt backend):

```lua
image:show()
```

One cool thing about this library is that all the functions can be cascaded.
Here's an example:

```lua
-- Open, transform and save back:
gm.Image('input.jpg'):flip():size(128):save('thumb.jpg')
```
