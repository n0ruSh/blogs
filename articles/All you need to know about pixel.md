# Device/Physical Pixel

Physical pixels on the device screen, of which there are a fixed amount on any device.

```
iMac (Retina 4K, 21.5-inch, 2017)

Physical Resolution: 4096 x 2304
```

# Logical pixel

Information that specifies the color at a particular location on a grid. This type of pixel has no inherent physical size.

```
window.screen.height
window.screen.width
```

```
iMac (Retina 4K, 21.5-inch, 2017)

Default Mode: Logical Resolution is 2048 * 1152
More Space Mode: Logical Resolution is 2560 * 1440
```

# CSS pixel

An abstraction layer created specifically for web developers to be used in CSS and JS (E.g. `border: 1px solid black` in CSS). It is independent of the device, used to measure pixels logically. Every CSS declaration and nearly every JS property works with CSS pixels, so in practice you'lll never use device/logical pixels. The only exception is `screen.width/height`.

# Viewport

## Layout viewport

The viewport relative to which the CSS layout is calculated, and which contains the layout.

## Visual viewport

The area of the site the user is currently seeing. The user can manipulate the visual viewport by zooming out or in, without affecting the layout viewport.

## Ideal viewport

The size of the layout viewport that is ideal for the device.

```html
<meta name="viewport" content="width=device-width" />
```

tell the browser to make the layout viewport to match the ideal viewport.

# Zooming

> screen.width / window.innerWidth

Zooming is the processing of enlarging CSS pixels. The more user zooms in, the more device/logical pixels are covered by one CSS pixel. One CSS pixel usually equals to one logical pixel without page zoom. Setting the zoom level to 200% will make one CSS pixel to equal to 4 physical pixels.

# Device Pixel Ratio (DPR)

Also referred to as CSS pixel ratio, is the ratio of the number of device pixels to the `ideal viewport size`. E.g.

| Device            | Physical Resolution | Device Pixel Ratio | Logical Resolution |
| ----------------- | ------------------- | ------------------ | ------------------ |
| iPhone 6          | 750 × 1334          | 2                  | 375 × 667          |
| iPhone X          | 1125 × 2436         | 3                  | 375 × 812          |
| iPad Pro          | 2048 × 2736         | 2                  | 1024 × 1368        |
| Samsung Galaxy S4 | 1080 × 1920         | 3                  | 360 × 640          |

```
window.devicePixelRatio
```

# Pixels per inch (PPI) - for physical resolution

Pixel density (also referred to as "screen density" or "display density") measures the density of device pixels in a given physical area.

E.g. iPhone X (5.8 英寸) with 1125 × 2436px in physical resolution

PPI = Math.sqrt(Math.pow(1125,2) + Math.pow(2436, 2)) / 5.8 = 462ppi

# Webview

A webview is an OS's browsering interface for native apps. E.g. A Twitter client may call on the platform's webview to show a webpage when the user clicks on a link in their feed. In general, webviews are seperate programs that use many low-level components (such as rendering engines) of the default browser, but may differ in other respects.

# 1x vs 2x vs 3x

In order for images to look their very best on high resolution screens, it's necessary to provide different image versions for different devicePixelRatios

Device Pixel Ratio Indicates that: On this device, an <img> tag with a CSS width of 250 pixels, will look best when the source image is..

1 1 device pixel = 1 CSS pixel 250 pixels wide
2 2 device pixels = 1 CSS pixel 500 pixels wide
3 3 device pixels = 1 CSS pixel 750 pixels wide

Things to note:

The pixel dimensions listed in image editors, file directories, and other places are a measurement of logical pixels.
For higher resolution screens and larger displays you'll need images with larger dimensions. Merely enlarging smaller images defeats the purpose of serving multiple image versions. The browser would have done this anyway if a high resolution image was not provided.

## Notice

- If you want to follow the latest news/articles for the series of my blogs, Please [「Watch」](https://github.com/n0ruSh/blogs/)to Subscribe
