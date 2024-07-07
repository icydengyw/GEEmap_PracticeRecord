## Comparison of mask, unmask, and updatemask


### image.unmask()

`image.unmask(number)`

The `unmask` function is used to replace the pixel values in masked areas with any specified value while keeping the values in unmasked areas unchanged. For example, `image.unmask(-9999)` will replace the values in the masked areas with `-9999`. This allows the image to be seamlessly compatible with ArcGIS.

### image.mask()

`image.mask()`

The `mask` function will differentiate the masked and unmasked areas of an image using 0 and 1, where 1 represents the masked areas and 0 represents the unmasked areas. Within the boundaries of the image, black represents the masked areas, and white represents the unmasked areas.

### image.updateMask()

`image.updateMask()`

The `updateMask` function will mask out areas of the image where the value is 0. If you want one image to have the same mask as another image, you can use `image2.updateMask(image1.mask().not())`.

In simpler terms: `image2` is the image you want to mask, and the boundary of `image1` is your study area (e.g., the planting area of a certain crop). If you need `image2` to only display the parts that overlap with `image1`, use `image2.updateMask(image1)`.
