# Настройки Нюка 15 для работы в ACES2065

Обкатано на проекте "Планета". С такими настройками получаем корректный цвет в ДаВинчи и в Нюке.

## Project Settings

- **color management:** OCIO
- **OCIO config:** fn-nuke_studio-config-v1.0.0_aces-v1:3_ocio-v2.1

default LUT settings

- **working space:** scene_linear (ACEScg)
- **Thumbnails:** ACES 1.0 - SDR Video (sRGB - Display)
- **Monitor Out:** ACES 1.0 - SDR Video (sRGB - Display)
- **8-bit files:** matte_paint (sRBG - Texture)
- **16-bit files:** texture_paint (ACEScct)
- **log files:** compositing_log (ACEScct)
- **float files:** scene_linear (ACEScg)

## Viewer

- **viewerProcess:** ACES 1.0 - SDR Video (Rec.1886 Rec.709 - Display)

## На входе

EXR файлы

- **Input Transform:** aces_interchange (ACES2065-1)

## На выходе

EXR файлы:

- **transform type:** colorspace
- **output transform:** aces_interchange (ACES2065-1)

MP4 файлы:

- **transform type:** display
- **display:** Rec.1886 Rec.709 - Display
- **view:** ACES 1.0 - SDR Video

ВАЖНО: Если нужны картинки JPG, PNG с тем цветом, как вы видите во вьювере — используйте настройки цвета как для MP4. Иначе цвет чуть другой.
