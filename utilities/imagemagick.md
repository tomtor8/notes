## Applying blur to images

```bash
magick input.jpg -blur 0x5 output_blurred.jpg
```

- `-blur 0x5` applies a Gaussian blur with an auto-calculated radius and a sigma of 5

### Blur operators

- `-blur {radius}x{sigma}`
- `radius` determines the size of the area for blurring
- setting it to `0` or omitting it lets ImageMagick automatically calculate a suitable radius based on the `sigma`
- `sigma` (standard deviation) is the most important argument; it controls the amount of blurring
- a larger sigma value results in a more pronounced blur

## Creating PDF files from images

```bash
magick *.jpg -auto-orient some-pdf-file.pdf
```

## Compress images

- to compress isolated images with conversion from png to jpg

```bash
magick image-name.png -quality 80 compressed-image-name.jpg
```

### Batch compress all png files and convert them to jpg files:

```bash
mogrify -quality 80 -format jpg *.png
```

If you want to remove the metadata and further reduce the image size use the `-strip` flag.

```bash
magick image-name.png -quality 80 -strip compressed-image-name.jpg
```

To compress the JPEG image even further, you can use the `-sampling-factor 4:2:0` flag, but be careful and check the results.

```bash
magick <in-img.name> -quality 80 -strip -sampling-factor 4:2:0 <out-img.name>
```

> [!WARNING]
> Be careful though because after applying `strip` or `sampling-factor` flags the actual image file can INCREASE instead of decrase. That's because imagemagick re-encodes the whole image and if the original image was too compressed the re-encoding can inflate the file size.

## Resizing images

- to resize an image for e.g. cover album art, use at most `300x300` size
- again it is useful to strip the metadata with `-strip`
- the quality can be reduced as well

```bash
magick image-name.png -resize 300x300 -quality 80 -strip compressed-image-name.jpg
```
