# UTIF.js
A small, fast and advanced TIFF decoder. It is the main TIFF decoder for [Photopea image editor](https://www.photopea.com). Try to open your TIFF file with Photopea to see, if UTIF.js can parse it.

* Supports Black & White, Grayscale, RGB and Paletted images
* Supports Fax 3 and Fax 4 (CCITT), LZW, PackBits and other compressions
* E.g. [this 8 MPix image](//www.photopea.com/api/img/G4.TIF) with Fax 4 compression has just 56 kB ( [Open in Photopea](https://www.photopea.com?p=%7B%22files%22:%5B%22//www.photopea.com/api/img/G4.TIF%22%5D%7D) )

#### `UTIF.decode(buffer)`
* `buffer`: ArrayBuffer containing the TIFF file
* returns an array of "images" ("layers", "pages"). Each element of this array is an object with following properties:
* * `width`: the width of the image
* * `height`: the height of the image
* * `data`: pixel data of the image
* * `tXYZ`: other TIFF tags

TIFF files may have different number of channels and different color depth. The interpretation of `data` depends on many tags (see the [TIFF 6 specification](http://www.npes.org/pdf/TIFF-v6.pdf)).

#### `UTIF.toRGBA8(img)`
* `img`: TIFF image object (returned by UTIF.decode())
* returns Uint8Array of the image in RGBA format, 8 bits per channel (ready to use in ctx.putImageData() etc.)

## Example

```javascript
function imgLoaded(buff) {
  var pages = UTIF.decode(buff);   console.log(pages[0]);
  var rgba  = UTIF.toRGBA8(pages[0]);  // Uint8Array with RGBA pixels
}

var xhr = new XMLHttpRequest();
xhr.open("GET", "my_image.tif");
xhr.responseType = "arraybuffer";
xhr.onload = imgLoaded;   xhr.send();
```

## Encoding TIFF images

You should not save images into TIFF format in the 21st century.

TIFF, just like many other formats, needs to store 16-bit and 32-bit integers. TIFF format does not have a fixed endianity, so every TIFF encoder should handle both Big and Little Endian files. It is just a little introduction into ugliness of this format.

While PNG files occur with two compression methods: None and Deflate, TIFF files occur with [12 compression methods](https://en.wikipedia.org/wiki/TIFF#TIFF_Compression_Tag). After you implement all these 12 compression methods (some of them are very nontrivial), you can come across files with other compression methods, which are not documented anywhere. Such files were generated by programs, that are not used anymore, developed by companies, that don't exist anymore.
