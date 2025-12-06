this is cam example I get from espressif git hub and fix error for ESP-IDF V5.5.1. 
add example stream video on ESP32's http sever

exam 1: read data from camera and print size of image
exam 2: stream video from camera to ESP's http sever (wifi AP mode)
exam 3: http server and capture single image 
# ESP32 Camera Driver

[![Build examples](https://github.com/espressif/esp32-camera/actions/workflows/build.yml/badge.svg)](https://github.com/espressif/esp32-camera/actions/workflows/build.yml) [![Component Registry](https://components.espressif.com/components/espressif/esp32-camera/badge.svg)](https://components.espressif.com/components/espressif/esp32-camera)
## General Information

This repository hosts ESP32 series Soc compatible driver for image sensors. Additionally it provides a few tools, which allow converting the captured frame data to the more common BMP and JPEG formats.

### Supported Soc

- ESP32
- ESP32-S2
- ESP32-S3

### Supported Sensor

| model   | max resolution | color type | output format                                                | Len Size |
| ------- | -------------- | ---------- | ------------------------------------------------------------ | -------- |
| OV2640  | 1600 x 1200    | color      | YUV(422/420)/YCbCr422<br>RGB565/555<br>8-bit compressed data<br>8/10-bit Raw RGB data | 1/4"     |
| OV3660  | 2048 x 1536    | color      | raw RGB data<br/>RGB565/555/444<br/>CCIR656<br/>YCbCr422<br/>compression | 1/5"     |
| OV5640  | 2592 x 1944    | color      | RAW RGB<br/>RGB565/555/444<br/>CCIR656<br/>YUV422/420<br/>YCbCr422<br/>compression | 1/4"     |
| OV7670  | 640 x 480      | color      | Raw Bayer RGB<br/>Processed Bayer RGB<br>YUV/YCbCr422<br>GRB422<br>RGB565/555 | 1/6"     |
| OV7725  | 640 x 480      | color      | Raw RGB<br/>GRB 422<br/>RGB565/555/444<br/>YCbCr 422         | 1/4"     |
| NT99141 | 1280 x 720     | color      | YCbCr 422<br/>RGB565/555/444<br/>Raw<br/>CCIR656<br/>JPEG compression | 1/4"     |
| GC032A  | 640 x 480      | color      | YUV/YCbCr422<br/>RAW Bayer<br/>RGB565                        | 1/10"    |
| GC0308  | 640 x 480      | color      | YUV/YCbCr422<br/>RAW Bayer<br/>RGB565<br/>Grayscale                         | 1/6.5"   |
| GC2145  | 1600 x 1200    | color      | YUV/YCbCr422<br/>RAW Bayer<br/>RGB565                        | 1/5"     |
| BF3005  | 640 x 480      | color      | YUV/YCbCr422<br/>RAW Bayer<br/>RGB565                        | 1/4"     |
| BF20A6  | 640 x 480      | color      | YUV/YCbCr422<br/>RAW Bayer<br/>Only Y                        | 1/10"    |
| SC101IOT| 1280 x 720     | color      | YUV/YCbCr422<br/>Raw RGB                                     | 1/4.2"   |
| SC030IOT| 640 x 480      | color      | YUV/YCbCr422<br/>RAW Bayer                                   | 1/6.5"   |
| SC031GS | 640 x 480      | monochrome | RAW MONO<br/>Grayscale                                       | 1/6"     |
| HM0360  | 656 x 496      | monochrome | RAW MONO<br/>Grayscale                                       | 1/6"     |
| HM1055  | 1280 x 720     | color      | 8/10-bit Raw<br/>YUV/YCbCr422<br/>RGB565/555/444             | 1/6"     |

## Important to Remember

- Except when using CIF or lower resolution with JPEG, the driver requires PSRAM to be installed and activated.
- Using YUV or RGB puts a lot of strain on the chip because writing to PSRAM is not particularly fast. The result is that image data might be missing. This is particularly true if WiFi is enabled. If you need RGB data, it is recommended that JPEG is captured and then turned into RGB using `fmt2rgb888` or `fmt2bmp`/`frame2bmp`.
- When 1 frame buffer is used, the driver will wait for the current frame to finish (VSYNC) and start I2S DMA. After the frame is acquired, I2S will be stopped and the frame buffer returned to the application. This approach gives more control over the system, but results in longer time to get the frame.
- When 2 or more frame bufers are used, I2S is running in continuous mode and each frame is pushed to a queue that the application can access. This approach puts more strain on the CPU/Memory, but allows for double the frame rate. Please use only with JPEG.
- The Kconfig option `CONFIG_CAMERA_PSRAM_DMA` enables PSRAM DMA mode on ESP32-S2 and ESP32-S3 devices. This flag defaults to false.
- You can switch PSRAM DMA mode at runtime using `esp_camera_set_psram_mode()`.

## Installation Instructions


### Using with ESP-IDF

- Add a dependency on `espressif/esp32-camera` component:
  ```bash
  idf.py add-dependency "espressif/esp32-camera"
  ```
  (or add it manually in idf_component.yml of your project)
- Enable PSRAM in `menuconfig` (also set Flash and PSRAM frequiencies to 80MHz)
- Include `esp_camera.h` in your code