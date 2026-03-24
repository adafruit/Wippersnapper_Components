---
name: add-component-v1
description: >
  Adds a new I2C sensor component definition to the Wippersnapper_Components repository.
  Use this skill when the user wants to add a new sensor component, create a definition.json,
  or add an image for a WipperSnapper I2C component. Covers: research, definition.json creation,
  product image acquisition, CI validation, and PR creation. This skill is for the components
  repo only — firmware driver changes belong in Adafruit_Wippersnapper_Arduino.
---

# Add I2C Sensor Component Definition to Wippersnapper_Components v1

This skill adds a new I2C sensor component to the `Wippersnapper_Components` repository.
Each component lives in `components/i2c/<sensor_name>/` and consists of:

1. `definition.json` — describes the sensor, its I2C addresses, and subcomponents
2. `image.jpg` (or `.png`) — product photo, 400x300px, 4:3 ratio, under 100KB

## Arguments

Accepts a sensor name as argument (e.g. `/add-component-v1 TMP119`).

The user may also provide: datasheet URL, product page URL, learn guide URL, vendor name,
I2C addresses, and what the sensor measures. If not provided, research them.

## Step 0 — Research the Sensor (MANDATORY before creating any files)

Gather this information before writing anything:

| What | How to find it |
|------|---------------|
| **Product page** | Web search "adafruit \<SENSOR\>" to find `https://www.adafruit.com/product/<ID>` |
| **Learn guide** | Found on the product page. Fetch the `.md?view=all` version for easy reading: `https://learn.adafruit.com/<guide-slug>.md?view=all` |
| **Vendor** | The chip manufacturer (e.g. "Bosch", "Texas Instruments", "Sensirion"), NOT "Adafruit" unless Adafruit designed the chip |
| **I2C addresses** | Datasheet, learn guide, or Arduino library. Include ALL possible addresses (ADDR pin configs) |
| **What it measures** | Map each reading to a subcomponent type from the reference table below |
| **Documentation URL** | Prefer Adafruit learn guide > manufacturer docs > datasheet URL |

### Valid subcomponent types

These are the only valid values (must match `components/i2c/schema.json` pattern):

| Subcomponent | What it measures | SI unit |
|---|---|---|
| `ambient-temp` | Ambient temperature | degC |
| `ambient-temp-fahrenheit` | Ambient temperature | degF |
| `humidity` | Relative humidity | %RH |
| `pressure` | Barometric pressure | hPa |
| `altitude` | Relative altitude | m |
| `co2` | CO2 concentration | ppm |
| `eco2` | Estimated CO2 | ppm |
| `tvoc` | Total VOCs | ppb |
| `gas-resistance` | Gas resistance | ohm |
| `light` | Light level | lux |
| `lux` | Light level (lux) | lux |
| `proximity` | Proximity | unitless |
| `voltage` | Voltage | V |
| `current` | Current | A |
| `raw` | Raw data | unitless |
| `pm10-std` | PM1.0 standard | ug/m3 |
| `pm25-std` | PM2.5 standard | ug/m3 |
| `pm100-std` | PM10.0 standard | ug/m3 |
| `unitless-percent` | Percentage | % |
| `object-temp` | Object/thermocouple temp | degC |
| `object-temp-fahrenheit` | Object/thermocouple temp | degF |
| `accelerometer` | Acceleration | m/s2 |
| `magnetic-field` | Magnetic field | uT |
| `orientation` | Orientation | degrees |
| `gyroscope` | Angular velocity | rad/s |
| `gravity` | Gravity | m/s2 |
| `acceleration` | Linear acceleration | m/s2 |
| `rotation` | Rotation | rad/s |
| `color` | Color | unitless |
| `voc-index` | VOC index | unitless |
| `nox-index` | NOx index | unitless |

**Temperature sensors** almost always include BOTH `ambient-temp` AND `ambient-temp-fahrenheit`.
The firmware base class handles the degC-to-degF conversion automatically.

## Step 1 — Create the component folder and definition.json

### Folder naming

- **Lowercase only** — CI rejects uppercase characters in paths
- Folder name must match the `strcmp()` string used in the firmware's `WipperSnapper_I2C.cpp`
- Example: `components/i2c/tmp119/`

### definition.json format

```json
{
  "displayName": "<Sensor Display Name>",
  "vendor": "<Chip Manufacturer>",
  "productURL": "https://www.adafruit.com/product/<ID>",
  "documentationURL": "https://learn.adafruit.com/<guide-slug>",
  "published": false,
  "i2cAddresses": ["0x48"],
  "subcomponents": ["ambient-temp", "ambient-temp-fahrenheit"]
}
```

#### Field rules

| Field | Required | Notes |
|-------|----------|-------|
| `displayName` | Yes | 3-24 characters. Usually the chip name (e.g. "TMP119", "BME280") |
| `vendor` | Yes | 3-24 characters. The chip manufacturer, not "Adafruit" |
| `i2cAddresses` | Yes | Array of hex strings. Include ALL possible addresses |
| `subcomponents` | Yes | Array of sensor type strings or objects |
| `productURL` | No | URI to product page. Prefer Adafruit product page |
| `documentationURL` | No | URI to documentation. Prefer Adafruit learn guide |
| `published` | No | Always set to `false` for new contributions. Adafruit sets `true` after release |
| `description` | No | 3-255 characters. Brief description of capabilities |

#### Subcomponent formats

**Simple format** — when the sensor type name is self-explanatory:
```json
"subcomponents": ["ambient-temp", "ambient-temp-fahrenheit", "pressure"]
```

**Object format** — when a custom display name is needed for clarity:
```json
"subcomponents": [
  { "displayName": "Ambient Light", "sensorType": "light" },
  { "displayName": "UV Count", "sensorType": "raw" }
]
```

Use objects when:
1. The type name is ambiguous (e.g. "light" could mean visible, UV, or IR)
2. Two readings share the same physical type — v1 schema forbids duplicate `sensorType`, so use
   `"raw"` for the second with a descriptive `displayName`
3. Non-standard units that don't match the Adafruit_Sensor SI unit
4. Clarity in the UI compared to the auto-generated label

**Examples from existing components:**
- **SGP41** (VOC + NOx + raw): `["voc-index", "nox-index", {"displayName": "Raw VOC Ticks (Reference)", "sensorType": "raw"}]`
- **TMP117** (temp only): `["ambient-temp", "ambient-temp-fahrenheit"]`
- **BME280** (multi): `["ambient-temp", "ambient-temp-fahrenheit", "humidity", "pressure", "altitude"]`

### Schema validation

The definition.json must validate against `components/i2c/schema.json`. Key constraints:
- `displayName`: string, 3-24 chars
- `vendor`: string, 3-24 chars
- `i2cAddresses`: array of strings
- `subcomponents`: array — each item is either a valid sensor type string OR an object with
  required `displayName` (string) and `sensorType` (valid sensor type string), optional `defaultPeriod`
- No additional properties allowed (`additionalProperties: false`)

## Step 2 — Add product image

### Requirements (enforced by CI)

| Property | Requirement |
|----------|-------------|
| Dimensions | Exactly 400x300 pixels |
| Aspect ratio | Must be exactly 4:3 |
| File size | Under 100KB (102400 bytes) |
| Format | `.jpg`, `.jpeg`, `.png`, `.gif`, or `.svg` |
| Filename | `image.<ext>` (lowercase) |
| Mimetype | Must match file extension |

### Choosing the right source image

1. **From Adafruit product API:** `https://www.adafruit.com/api/product/<PID>` — the
   `product_image` field gives the default image URL (typically `640x480`).
2. **Use the original resolution source** for best quality when downscaling. The Adafruit CDN
   URL contains a resolution prefix that can be replaced:
   - `https://cdn-shop.adafruit.com/original/<PID>-NN.jpg` — **original source (always use this)**
   - `https://cdn-shop.adafruit.com/970x728/<PID>-NN.jpg` — high res
   - `https://cdn-shop.adafruit.com/640x480/<PID>-NN.jpg` — medium (API default)
   - Where `NN` is the image number (`01` = default, `00`-`04` are common variants)
   - Simply replace the resolution part (e.g. `640x480`) with `original` in any CDN URL
3. **Check all available shots** by testing `00` through `04` — pick the straight-on close-up
   of the breakout board with a plain background. Avoid lifestyle shots, angled shots with other
   boards, or images where the board is very small in the frame.
4. Compare with existing component images (e.g. `components/i2c/sgp41/image.jpg`) for the
   expected style: close-up, slight angle, plain dark background, board well-centered.

### Centering and cropping

**The board must be visually centered in the final image** — roughly equal space between the
board edges and the image borders on all sides. The source image from Adafruit often has the
board slightly off-center.

To correct centering:
1. Identify where the board sits in the source image (estimate top/bottom/left/right margins)
2. Apply a **vertical shift crop** — crop from the top to push the board down, or vice versa
3. Then crop horizontally to restore exact 4:3 ratio
4. Aim for the board top and bottom being approximately the same distance from the image borders

Example using Python PIL:
```python
from PIL import Image

img = Image.open('source_970x728.jpg')
w, h = img.size

# Shift crop down by N pixels to center the board vertically
shift = 18  # adjust based on visual inspection
cropped = img.crop((0, shift, w, h))

# Restore exact 4:3 ratio by trimming width symmetrically
target_h = cropped.size[1]
target_w = int(target_h * 4 / 3)
target_w = target_w - (target_w % 2)  # ensure even
x_offset = (cropped.size[0] - target_w) // 2
final = cropped.crop((x_offset, 0, x_offset + target_w, target_h))
```

### High-quality resize

**Avoid resize artifacts** — use the best available downscaling filter and start from the
highest resolution source image.

```python
from PIL import Image

# LANCZOS is the highest quality downscale filter in PIL
resized = final.resize((400, 300), Image.LANCZOS)

# Save as high-quality JPEG (will be compressed by mozjpeg next)
resized.save('temp_highquality.jpg', 'JPEG', quality=92, subsampling=0)
```

Key points:
- Always use `Image.LANCZOS` (not `BILINEAR` or `NEAREST`) for downscaling
- Use `quality=92` and `subsampling=0` (4:4:4 chroma) for the intermediate JPEG
- Start from the largest source image — downscaling from 970x728 to 400x300 produces far
  fewer artifacts than from 640x480
- Never use `!` force-resize without cropping to 4:3 first — it stretches the image

### Compression with mozjpeg

Use **mozjpeg** (https://github.com/mozilla/mozjpeg) for best JPEG compression. The Python
package `mozjpeg-lossless-optimization` provides lossless re-encoding that typically saves
10-15% over standard JPEG encoders.

```bash
pip install mozjpeg-lossless-optimization
```

```python
import mozjpeg_lossless_optimization as mozjpeg

with open('temp_highquality.jpg', 'rb') as f:
    input_data = f.read()

optimized = mozjpeg.optimize(input_data)

with open('components/i2c/<sensor_name>/image.jpg', 'wb') as f:
    f.write(optimized)
```

If the result is still over 100KB, reduce the PIL quality setting (try 85, then 80) and
re-run mozjpeg optimization. Delete the intermediate `temp_highquality.jpg` after.

**Fallback** if mozjpeg is unavailable: use PIL directly with `quality=85` — this usually
produces files under 100KB for 400x300 product photos.

### Complete image processing script

```python
from PIL import Image
import urllib.request, io, os

# 1. Download high-res source
PID = '<product_id>'
IMAGE_NUM = '01'  # try 00-04 to find best shot
url = f'https://cdn-shop.adafruit.com/original/{PID}-{IMAGE_NUM}.jpg'
data = urllib.request.urlopen(url).read()
img = Image.open(io.BytesIO(data))

# 2. Center-crop (adjust shift based on visual inspection)
w, h = img.size
shift = 18  # pixels to shift down; use negative to shift up
if shift > 0:
    cropped = img.crop((0, shift, w, h))
else:
    cropped = img.crop((0, 0, w, h + shift))

# 3. Restore exact 4:3 ratio
target_h = cropped.size[1]
target_w = int(target_h * 4 / 3)
target_w = target_w - (target_w % 2)
x_offset = (cropped.size[0] - target_w) // 2
final = cropped.crop((x_offset, 0, x_offset + target_w, target_h))

# 4. High-quality resize
resized = final.resize((400, 300), Image.LANCZOS)

# 5. Save intermediate high-quality JPEG
temp_path = 'temp_highquality.jpg'
resized.save(temp_path, 'JPEG', quality=92, subsampling=0)

# 6. Optimize with mozjpeg
try:
    import mozjpeg_lossless_optimization as mozjpeg
    with open(temp_path, 'rb') as f:
        optimized = mozjpeg.optimize(f.read())
    output_path = f'components/i2c/<sensor_name>/image.jpg'
    with open(output_path, 'wb') as f:
        f.write(optimized)
    print(f'Saved: {len(optimized)/1024:.1f} KB')
except ImportError:
    # Fallback: just use the PIL output
    resized.save(f'components/i2c/<sensor_name>/image.jpg', 'JPEG', quality=85)

# 7. Clean up temp file
os.remove(temp_path)
```

### Platform notes

- **Windows:** `/tmp` does not exist. Use `tempfile.gettempdir()` or write temp files to the
  working directory and clean up after. When using `python.exe` on Windows, use Windows-style
  paths (`C:/...`) not Unix paths (`/tmp/...`).
- **ImageMagick** may not be installed. Prefer Python PIL/Pillow which is `pip install Pillow`.
- Always clean up intermediate files (candidates, test crops, BMPs) — only `image.jpg` should
  remain in the component folder.

## Step 3 — Validate locally (if possible)

### JSON schema validation
```bash
# Using ajv-cli (npm install -g ajv-cli)
ajv validate -s components/i2c/schema.json -d "components/i2c/<sensor_name>/definition.json"

# Or using Python
python -c "
import json, jsonschema
schema = json.load(open('components/i2c/schema.json'))
defn = json.load(open('components/i2c/<sensor_name>/definition.json'))
jsonschema.validate(defn, schema)
print('Valid!')
"
```

### Image validation
```bash
# Check dimensions
magick identify -ping -format "%wx%h" components/i2c/<sensor_name>/image.jpg
# Should output: 400x300

# Check file size (must be under 102400 bytes)
stat -c%s components/i2c/<sensor_name>/image.jpg  # Linux
stat -f%z components/i2c/<sensor_name>/image.jpg  # macOS
wc -c < components/i2c/<sensor_name>/image.jpg    # cross-platform
```

### CI checks that will run on PR

1. **JSON schema validation** — definition.json against schema.json
2. **Filename validation** — only `definition.json` and `image.(png|jpg|jpeg|gif|svg)` allowed,
   all lowercase, under `components/<type>/<name>/`
3. **Image mimetype** — file extension must match actual mimetype
4. **Image dimensions** — max 400x300, must be 4:3 ratio
5. **Image file size** — under 100KB
6. **URL validation** — productURL and documentationURL must be valid URIs

## Step 4 — Create PR

Branch from `main`, commit the component folder, and create a PR.

**PR title:** `Add <SENSOR> component definition`

**PR body should include:**
- Description of the component
- Product URL
- Documentation URL
- Reference to companion firmware PR if applicable (in `adafruit/Adafruit_Wippersnapper_Arduino`)

Use the repo's PR template which asks for: Description, Product URL, and Product Documentation URL.

---

## Worked Example: TMP119

### Research
- Web search "adafruit TMP119" -> product page https://www.adafruit.com/product/6482
- Product page links to learn guide: https://learn.adafruit.com/adafruit-tmp119-high-precision-temperature-sensor
- Vendor: Texas Instruments (TMP119 is a TI chip)
- I2C addresses: 0x48, 0x49, 0x4A, 0x4B (from datasheet, same as TMP117)
- Measures: Temperature only -> `ambient-temp` + `ambient-temp-fahrenheit`
- Closest existing component: `components/i2c/tmp117/`

### Files created

`components/i2c/tmp119/definition.json`:
```json
{
  "displayName": "TMP119",
  "vendor": "Texas Instruments",
  "productURL": "https://www.adafruit.com/product/6482",
  "documentationURL": "https://learn.adafruit.com/adafruit-tmp119-high-precision-temperature-sensor",
  "published": false,
  "i2cAddresses": ["0x48", "0x49", "0x4A", "0x4B"],
  "subcomponents": ["ambient-temp", "ambient-temp-fahrenheit"]
}
```

`components/i2c/tmp119/image.jpg` — 400x300px product photo from Adafruit, under 100KB.

### Companion PR
Firmware driver PR in `adafruit/Adafruit_Wippersnapper_Arduino` — references this components PR.
