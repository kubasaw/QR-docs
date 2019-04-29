# QR Code API  <!-- omit in toc -->

This document describes methods, parameters and usage of Fast QR Code Generator API.

[<img width="215" alt="Connect on RapidAPI" src="https://storage.googleapis.com/code-snippets/connect-on-rapidapi-light.png">](https://rapidapi.com/kubasaw/api/qrcode3)

## Table of Contents <!-- omit in toc -->
- [API usage methods](#api-usage-methods)
  - [Simple QR Code generation](#simple-qr-code-generation)
  - [QR Code generation with image upload](#qr-code-generation-with-image-upload)
  - [QR Code generation with separately uploaded image](#qr-code-generation-with-separately-uploaded-image)
- [API parameters](#api-parameters)
  - [Basic parameters](#basic-parameters)
  - [Appearance parameters](#appearance-parameters)
  - [QR Code specific parameters](#qr-code-specific-parameters)
- [Styles' list](#styles-list)
- [Eyes styles' list](#eyes-styles-list)

## API usage methods

This API offers three similar workflows for QR Code generation. All of those differs in the way of handling image which may be embedded into QR.   

### Simple QR Code generation
The basic scheme of usage is based on `/generateQR` API endpoint. All parameters listed below are available. For `image` parameter expected value is (encoded) URL of image which should be embedded in QR Code. Endpoint is accesible via HTTP GET method. Only valid URLs pointing to `*.svg`, `*.jpeg`, `*.jpg`, `*.png` and `*.gif` files are allowed.

![Simple QR Code generation](https://raw.githubusercontent.com/kubasaw/QR-docs/master/schemes/generate.svg?sanitize=true)

### QR Code generation with image upload
If custom image should be placed on the QR and it is not accesible via external URL, POST multipart request to `/generateQRwithLogo` endpoint can be used. As far as big overhead is caused by usage of multipart method, it is not recommended to use this method for multiple codes generation with the same image, but it is the best solution for single QR generation with custom image. All parameters listed in the tables below are also available. For upload, `*.svg`, `*.jpeg`, `*.jpg`, `*.png` and `*.gif` files with maximum size of 1MB are allowed.

![QR Code generation with image upload](https://raw.githubusercontent.com/kubasaw/QR-docs/master/schemes/image+generate.svg?sanitize=true)

### QR Code generation with separately uploaded image
If custom image will be used for batch generation of QRs, this image can be previously uploaded via `/uploadLogo` endpoint. Every uploaded image will be available for 24 hours after last request involving this image. `/uploadLogo` endpoint responses with identifier which can be used for future request to `/generateQR` endpoint with this identifier specifed as `image` parameter. This workflow is recommended for batch generation of QRs or dynamic generation as it guarantees lowest possible response time and data overhead. For upload, `*.svg`, `*.jpeg`, `*.jpg`, `*.png` and `*.gif` files with maximum size of 1MB are allowed.

![QR Code generation with separately uploaded image](https://raw.githubusercontent.com/kubasaw/QR-docs/master/schemes/upload+generate.svg?sanitize=true)

## API parameters

### Basic parameters
| Parameter | Type             | Default value           | Accepted values                 | Description                                                                                                                                                                                                                                                                                                   |
| --------- | ---------------- | ----------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| text      | `string`         | **required in request** | `string` of (almost) any length | Text which will be encoded in QR. Value have to be URL encoded. Maximum length which can be encoded in QR as UTF-8 with the lowest error correction level is 2953 bytes, resulting in 177x177 modules code. For more information please refer to: [QRcode.com](https://www.qrcode.com/en/about/version.html). |
| size      | (`float`\|`int`) | 150                     | <100;2500>                      | Default size (width and height) in pixels for generated image of QR Code. If requested value is `float` it will be casted to nearest `int`.                                                                                                                                                                   |
| format    | `string`         | `svg`                   | {`svg`, `png`, `pdf`, `jpg`}    | File format for generated QR.                                                                                                                                                                                                                                                                                 |


### Appearance parameters
| Parameter                                               | Type               | Default value | Accepted values                                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------------------------------------- | ------------------ | ------------- | ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| style                                                   | `string`           | `default`     | **see [Styles' list](#styles-list)**            | Style applied to QR Code modules. Please refer to table below where all available styles are listed.                                                                                                                                                                                                                                                                                                                                                                                                                        |
| <a name="style_color">style\_color</a>                  | `string`           | `#000000`     | <`#000000`; `#FFFFFF`>                          | Base color for QR Code modules referred as RGB triple value presented as hex number. Can be obtained from [w3schools.com](https://www.w3schools.com/colors/colors_picker.asp).                                                                                                                                                                                                                                                                                                                                              |
| inner\_eye\_style                                       | `string`           | `default`     | **see [Eyes styles' list](#eyes-styles-list)**  | Style applied to inner QR Code eyes. Please refer to table below where all available styles are listed.                                                                                                                                                                                                                                                                                                                                                                                                                     |
| <a name="inner_eye_color">inner\_eye\_color</a>         | `string`           | `None`        | <`#000000`; `#FFFFFF`>                          | Base color for inner QR Code eyes. If this value is not specified, base color is inherited from data modules color (see parameter: [style\_color](#style_color)).                                                                                                                                                                                                                                                                                                                                                           |
| outer\_eye\_style                                       | `string`           | `default`     | **see [Eyes styles' list](#eyes-styles-list)**  | Style applied to outer QR Code eyes. Please refer to table below where all available styles are listed.                                                                                                                                                                                                                                                                                                                                                                                                                     |
| <a name="outer_eye_color">outer\_eye\_color</a>         | `string`           | `None`        | <`#000000`; `#FFFFFF`>                          | Base color for inner QR Code eyes. If this value is not specified, base color is inherited from data modules color (see parameter: [style\_color](#style_color)).                                                                                                                                                                                                                                                                                                                                                           |
| bg\_color                                               | `string`           | `#FFFFFF`     | <`#000000`; `#FFFFFF`>                          | Background color for QR Code. If value is not specified, background area is removed resulting in transparent (format `png` or `svg`) or white (format `pdf` or `jpg`) background depending on choosen file format.                                                                                                                                                                                                                                                                                                          |
| <a name="fill_style">fill\_style</a>                    | `string`           | `solid`       | {`solid`, `linearGradient`, `radialGradient`}   | Type of whole QR Code fill style. If gradient-style fill is requested, fill color of modules changes fluently from [style\_color](#style_color) to [gradient\_stop\_color](#gradient_stop_color). In case of `linearGradient` color changes along straight line and in case of `radialGradient` color changes along radius from center of QR. If [outer\_eye\_color](#outer_eye_color) or [inner\_eye\_color](#inner_eye_color) parameter are specified, they override gradient behaviour on according outer or inner eyes. |
| <a name="gradient_stop_color">gradient\_stop\_color</a> | `string`           | `#000000`     | <`#000000`; `#FFFFFF`>                          | Second (stop) color of gradient style fill (see parameter: [fill\_style](#fill_style))                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| gradient\_angle                                         | (`float`\|`int`)   | 0             | <0;360)                                         | Angle (in degrees) of linear gradient rotation measured clockwise. By default, horizontal gradient is applied. To reverse color direction use value of 180. Vertical gradients are indicated by values of 90 and 180 etc.                                                                                                                                                                                                                                                                                                   |
| image                                                   | (`string`\|`file`) | `None`        | **see [API Usage Methods](#api-usage-methods)** | Image file to be embedded into QR code                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| remove\_background                                      | `boolean`          | `false`       | {`true`,`false`}                                | If set to `true`, all QR code modules behind embedded image area are removed. This option may be useful in case of using image with transparent background and/or irregular shape.                                                                                                                                                                                                                                                                                                                                          |

### QR Code specific parameters

| Parameter   | Type   | Default value | Accepted values      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ----------- | ------ | ------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| ec\_level   | `char` | `M`           | {`L`, `M`, `Q`, `H`} | Error correction level for generated QR. Allows to restore some encoded data if it is partially unreadable. Literals correspond to amount of data which can be restored:<ul><li>`L`: approx. 7%</li><li>`M`: approx. 15%</li><li>`Q`: approx. 25%</li><li>`H`: approx. 30%</li></ul>Higher correction level causes in bigger resulting QR. **If image is embedded in QR, `H` correction level is choosen automatically and overrides user input.** For more information please refer to: [QRcode.com](https://www.qrcode.com/en/about/error_correction.html) |
| quiet\_zone | `int`  | 4             | <0;4>                | Default size of margin with background color around generated QR Code. This length is expressed as number of modules of Code. Recommended value is 4, but most readers allow you to read the code with smaller or zero margin. For more information please refer to: [QRcode.com](https://www.qrcode.com/en/howto/code.html).                                                                                                                                                                                                                                |

## Styles' list

| Name         | Style example                                                                                            |
| ------------ | -------------------------------------------------------------------------------------------------------- |
| `default`    | ![Style: default](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/styles/default.png)       |
| `arrow`      | ![Style: arrow](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/styles/arrow.png)           |
| `circle`     | ![Style: circle](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/styles/circle.png)         |
| `classic`    | ![Style: classic](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/styles/classic.png)       |
| `heavyround` | ![Style: heavyround](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/styles/heavyround.png) |
| `lightround` | ![Style: lightround](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/styles/lightround.png) |
| `sieve`      | ![Style: sieve](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/styles/sieve.png)           |

## Eyes styles' list
| Name         | Inner eye                                                                                                    | Outer eye                                                                                                    |
| ------------ | ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------ |
| `default`    | ![Style: default](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/default_inner.png)       | ![Style: default](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/default_outer.png)       |
| `circle`     | ![Style: circle](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/circle_inner.png)         | ![Style: circle](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/circle_outer.png)         |
| `cushion`    | ![Style: cushion](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/cushion_inner.png)       | ![Style: cushion](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/cushion_outer.png)       |
| `diamond`    | ![Style: diamond](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/diamond_inner.png)       | ![Style: diamond](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/diamond_outer.png)       |
| `dots`       | ![Style: dots](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/dots_inner.png)             | ![Style: dots](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/dots_outer.png)             |
| `heavyround` | ![Style: heavyround](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/heavyround_inner.png) | ![Style: heavyround](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/heavyround_outer.png) |
| `leaf`       | ![Style: leaf](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/leaf_inner.png)             | ![Style: leaf](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/leaf_outer.png)             |
| `left\_eye`  | ![Style: left\_eye](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/left_eye_inner.png)    | ![Style: left\_eye](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/left_eye_outer.png)    |
| `right\_eye` | ![Style: right\_eye](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/right_eye_inner.png)  | ![Style: right\_eye](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/right_eye_outer.png)  |
| `shield`     | ![Style: shield](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/shield_inner.png)         | ![Style: shield](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/shield_outer.png)         |
| `sieve`      | ![Style: sieve](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/sieve_inner.png)           | ![Style: sieve](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/sieve_outer.png)           |
| `star`       | ![Style: star](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/star_inner.png)             | ![Style: star](https://raw.githubusercontent.com/kubasaw/QR-docs/master/img/eyes/star_outer.png)             |


**Acknowledgement:** The word *QR Code* is registered trademark of [DENSO WAVE INCORPORATED](http://www.denso-wave.com/en/en/) in Japan and other countries.
