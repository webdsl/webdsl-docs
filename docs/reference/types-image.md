# Types - Image

Represents an (uploaded) image. The page input for an Image is a file upload component. The page output shows the image.

---

Functions
----

**fileName():String**

Returns the name of this image file.

**getContentAsString():String**

Returns the content of this file as String.

**download()**

The current action will result in a download of this image file.

**getWidth()**

Returns the calculated width of this image.

**getHeight()**

Returns the calculated height of this image.

**resize(maxWidth : Int, maxHeight : Int)**

Resizes the image to the set dimensions. Note that currently, images can only be downscaled.

**crop(x : Int, y : Int, width : Int, height : Int)**

Crops the image to the specified size and coordinates.

**clone() : Image**

Makes a copy of the image and returns it.

