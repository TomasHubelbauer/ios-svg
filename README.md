# iOS SVG

[**LIVE**](https://tomashubelbauer.github.io/ios-svg)

I experiment to see if an SVG saved directly from a site (using long tap) or by
an `a[download]` link is downloadable to iOS Photos and vieweable in there. Also
I want to find out if when uploaded back the SVG is in any way altered by iOS.

I do this because I want to explore SVG image export as a backup mechanism for
a mobile web app.

---

`svg` element has no long tap menu so no Save Image option.

`img` element pointing to an SVG file has a Save Image long tap option but the
image doesn't appear in Photos afterwards - it doesn't work.

`a` element leading to an SVG file opens it in Safari with Save Image available
under the Share button, but it doesn't work either, nothing appears in Photos.
**However** a Save to Files option is available in the Share menu as well.
This option is available for files that Safari can't display and SVGs (which it
can display) but not files it can display (text files, HTML, …).

On Wikipedia Save Image on an SVG works because it serves a pre-rendered PNG.

I tried generating a PNG using `canvas` and appending Base64 encoded data to it
but it breaks the image preview in Safari and as a result the Save Image option
is no longer presented.

I will instead try to use a PNG textual chunk as those can go up to 2 gigabytes:

> Text string can be of any length from zero bytes up to the maximum permissible
> chunk size less the length of the keyword and separator.

http://www.libpng.org/pub/png/spec/1.2/PNG-Chunks.html

> Every chunk has the same structure: a 4-byte length, a 4-byte chunk type, 0 to
> 2147483647 bytes of chunk data, and a 4-byte cyclic redundancy check value.

http://www.libpng.org/pub/png/book/chapter08.html

---

Right now I have a PNG generator which is capable of inserting a tEXt chunk with
JSON before the IEND chunk and infrastructure that allows the user to reupload
the downloaded image for checking if the contents of the chuck got disrupted.

## Tests

Here is a table where I capture sizes of payload that I checked.

| Length | Success |
|--------|---------|
| 4011   | Yes     |

> Length is the byte length of the `tEXt` chunk payload without the keyword and
> the `NUL` character (the JSON bytes only)

## To-Do

Find the largest chunk that will not get corrupted by iOS.

Replace `String.fromCharCode` with something else when it starts failing because
the spread arguments to it are too many. Probably a look which does that for
chunks of maximal accepted length and then just concatenates the intermediate
results will be enough.

Cross-reference or merge with the `ios-photos-metadata` repository.

Use `a[download]` when iOS 13 is out and it is supported in Safari.

See if using a non-standard keyword (like the program name) will have the same
treatment or if it will be handled differently by iOS.
