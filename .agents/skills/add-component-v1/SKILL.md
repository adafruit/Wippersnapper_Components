---
name: add-component-v1
description: >
  Adds a new I2C sensor component definition to the Wippersnapper_Components repository.
  Use this skill whenever the user wants to create a matching PR from the firmware repo,
  add a new sensor component, create a definition.json, add an image for a WipperSnapper
  I2C component, or even just mentions a sensor name in the context of WipperSnapper or
  Adafruit IO. Also use when the user says "add component", "matching PR", "new sensor",
  "component definition", or asks about what subcomponents or I2C addresses a sensor uses.
  Covers: research, definition.json creation, product image acquisition, CI
  validation, and PR creation. This skill is for the components repo only — firmware driver
  changes belong in Adafruit_Wippersnapper_Arduino (see the add-sensor-component-v1 skill there).
---

# Add I2C Sensor Component Definition to Wippersnapper_Components v1

This skill adds a new I2C sensor component to the `Wippersnapper_Components` repository.
Each component lives in `components/i2c/<sensor_name>/` and consists of:

1. `definition.json` — describes the sensor, its I2C addresses, and subcomponents
2. `image.jpg` (or `.png`) — product photo, 400x300px, 4:3 ratio, under 100KB

### Naming convention

- **lowercase** for the component folder name and all file names within it
- The folder name must exactly match the `strcmp()` string used in the firmware's
  `WipperSnapper_I2C.cpp` `initI2CDevice()` method
- Example: `components/i2c/tmp119/`

Decide the canonical name in Step 0 and use it everywhere.

## Reference

The official Adafruit guide for this process:
- Human-readable: https://learn.adafruit.com/how-to-add-a-new-component-to-adafruit-io-wippersnapper?view=all
- Machine-readable: https://learn.adafruit.com/how-to-add-a-new-component-to-adafruit-io-wippersnapper.md?view=all

Fetch the `<guide-slug-without-page-path-qualifier>.md?view=all` version if you need more detail on any step.

## Arguments

Accepts a sensor name as argument (e.g. `/add-component-v1 TMP119`).

The user may also provide: datasheet URL, product page URL, learn guide URL, vendor name,
I2C addresses, and what the sensor measures. If not provided, research them.

The user may also reference a companion firmware PR that contains a suggested `definition.json`.
**Treat PR-provided definitions as hints, not as verified data.** Always run Step 0 research
independently — firmware PR authors often use GitHub library URLs instead of learn guides,
miss I2C addresses, or use suboptimal field values. Every field must be verified against
authoritative sources (product page, learn guide, datasheet) before use.

## Step 0 — Research the Sensor (MANDATORY before creating any files)

> **Do not skip this step.** Even if a firmware PR or the user supplies a complete
> `definition.json`, you MUST independently verify every field — especially `documentationURL`
> (must be a learn guide for Adafruit products, not a GitHub repo) and `i2cAddresses` (must
> include all ADDR pin combinations). Copying unverified values from PRs has caused bad PRs.

### Check for duplicates first

Before anything else, check `components/i2c/` to see if a component already exists for this
sensor. If it does, tell the user — they may want to update it rather than create a new one.

### Gather sensor information

| What | How to find it |
|------|---------------|
| **Product page** | Web search "adafruit \<SENSOR\>" to find `https://www.adafruit.com/product/<ID>`. For non-Adafruit products, find the manufacturer's product page or a distributor listing (DigiKey, Mouser, etc.) |
| **Learn guide** | Found on the Adafruit product page HTML (grep for `learn.adafruit.com`). Fetch the `.md?view=all` version for easy reading. For non-Adafruit products, use the manufacturer's documentation or datasheet |
| **Vendor** | The chip manufacturer (e.g. "Bosch", "Texas Instruments", "Sensirion", "OMRON", "Melexis"), NOT "Adafruit" unless Adafruit designed the chip |
| **I2C addresses** | Datasheet, learn guide, or Arduino library. Include ALL possible addresses from ADDR pin configurations. Also check https://learn.adafruit.com/i2c-addresses/the-list |
| **What it measures** | Map each reading to a subcomponent type from the reference table below |
| **Documentation URL** | Prefer: Adafruit learn guide > manufacturer docs page/wiki > datasheet URL |
| **Closest existing component** | Browse `components/i2c/` for a sensor in the same family or with identical reading types — use it as a template |

### Valid subcomponent types

These are the only valid values (must match the pattern in `components/i2c/schema.json`):

| Subcomponent | What it measures | SI unit |
|---|---|---|
| `ambient-temp` | Ambient temperature | degC |
| `ambient-temp-fahrenheit` | Ambient temperature | degF |
| `object-temp` | Object/thermocouple temp | degC |
| `object-temp-fahrenheit` | Object/thermocouple temp | degF |
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
| `color` | Color | unitless |
| `raw` | Raw data | unitless |
| `unitless-percent` | Percentage | % |
| `pm10-std` | PM1.0 standard | ug/m3 |
| `pm25-std` | PM2.5 standard | ug/m3 |
| `pm100-std` | PM10.0 standard | ug/m3 |
| `pm10-env` | PM1.0 environmental | ug/m3 |
| `pm25-env` | PM2.5 environmental | ug/m3 |
| `pm100-env` | PM10.0 environmental | ug/m3 |
| `accelerometer` | Acceleration | m/s2 |
| `magnetic-field` | Magnetic field | uT |
| `orientation` | Orientation | degrees |
| `gyroscope` | Angular velocity | rad/s |
| `gravity` | Gravity | m/s2 |
| `acceleration` | Linear acceleration | m/s2 |
| `rotation` | Rotation | rad/s |
| `voc-index` | VOC index | unitless |
| `nox-index` | NOx index | unitless |

**Temperature sensors** almost always include BOTH `ambient-temp` AND `ambient-temp-fahrenheit`.
The firmware base class handles the degC-to-degF conversion automatically — the driver only
implements the Celsius version.

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
| `productURL` | No | URI to product page (see URL guidance below) |
| `documentationURL` | No | URI to documentation (see URL guidance below) |
| `published` | No | Always set to `false` for new contributions. Adafruit sets `true` after release |
| `description` | No | 3-255 characters. Brief description of capabilities |

#### URL guidance

**`productURL`** — where someone can buy or learn about the product:
- **Adafruit products:** `https://www.adafruit.com/product/<ID>` (preferred)
- **Non-Adafruit products:** Use distributor listings (DigiKey, Mouser) or manufacturer product
  pages. Examples from existing components:
  - DigiKey: `https://www.digikey.com/en/products/detail/...` (used by SEN5x, SHT20, MLX90632)
  - Manufacturer: `http://www.aosong.com/en/products-60.html` (used by AHT21)

**`documentationURL`** — technical docs for the sensor:
- **Adafruit products:** Adafruit learn guide (preferred). Find it on the product page
  (search for `learn.adafruit.com`). **Never use a GitHub library repo URL** (e.g.
  `github.com/adafruit/Adafruit_<SENSOR>`) — that is source code, not documentation.
- **Non-Adafruit products:** Manufacturer documentation page, wiki, or datasheet PDF URL.
  Examples: Sensirion datasheet PDFs, OMRON user manuals
- Third-party domain URLs may initially fail CI URL validation until a maintainer adds the
  domain to the allowlist — note this in the PR if using a non-standard domain

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
1. **Type name is ambiguous** — "light" could mean visible, UV, or IR. `displayName` clarifies
   in the UI
2. **Two readings share the same physical type** — v1 schema forbids duplicate `sensorType`.
   Use `"raw"` for the second with a descriptive `displayName` (e.g. LTR-329: `"light"` for
   ambient, `"raw"` with `displayName: "Infrared"` for IR)
3. **Non-standard units** — sensor reports in a unit that doesn't match the Adafruit_Sensor SI
   unit for that type; use `raw` or `unitless-percent` with a descriptive `displayName`
4. **Clarity** — when the auto-generated UI label would be confusing. Compare existing components
   that use the same types for reference

When using `"raw"` as a stand-in, the driver must implement `getEventRaw()` for that reading.

**Examples from existing components:**
- **TMP117** (temp only): `["ambient-temp", "ambient-temp-fahrenheit"]`
- **BME280** (multi): `["ambient-temp", "ambient-temp-fahrenheit", "humidity", "pressure", "altitude"]`
- **SGP41** (VOC + NOx + raw): `["voc-index", "nox-index", {"displayName": "Raw VOC Ticks (Reference)", "sensorType": "raw"}]`
- **D6T-1A** (ambient + object temp, all objects for clarity):
  ```json
  [
    {"displayName": "Ambient Temperature (°C)", "sensorType": "ambient-temp"},
    {"displayName": "Ambient Temperature (°F)", "sensorType": "ambient-temp-fahrenheit"},
    {"displayName": "Measured Object Temp (°C)", "sensorType": "object-temp"},
    {"displayName": "Measured Object Temp (°F)", "sensorType": "object-temp-fahrenheit"}
  ]
  ```
- **SEN55** (many readings, simple strings): `["ambient-temp", "ambient-temp-fahrenheit", "humidity", "pm10-std", "pm25-std", "pm100-std", "voc-index", "nox-index"]`

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

**For Adafruit products:**

1. **Scrape all image URLs from the product page:** Fetch `https://www.adafruit.com/product/<PID>`
   and extract all `cdn-shop.adafruit.com` image URLs. The product page gallery contains every
   available shot — don't guess image numbers, as they are not necessarily contiguous.
2. **CRITICAL — Always use the `/original/` CDN URL, NOT `970x728` or `640x480`.**
   The Adafruit CDN URL contains a resolution prefix. You MUST replace it with `original`
   to get the highest-resolution source for downscaling. Downloading a pre-scaled version
   (e.g. `970x728`) produces a blurry 400x300 result.
   - **USE THIS:** `https://cdn-shop.adafruit.com/original/<PID>-NN.jpg`
   - NOT: `https://cdn-shop.adafruit.com/970x728/<PID>-NN.jpg`
   - NOT: `https://cdn-shop.adafruit.com/640x480/<PID>-NN.jpg`
   - For each image URL scraped from the product page, replace the resolution part
     (e.g. `970x728`, `640x480`) with `original` before downloading
3. **Check all available shots** from the scraped URLs — pick the slight-angle / isometric
   close-up of the breakout board with a plain background (this is the standard Adafruit product
   photo style, typically image `00`). Avoid lifestyle shots, shots with other boards or
   accessories in the frame, or images where the board is very small in the frame.
4. Compare with existing component images (e.g. `components/i2c/sgp41/image.jpg`) for the
   expected style: close-up, slight angle, plain dark background, board well-centered.

**For non-Adafruit products:**

If no Adafruit CDN image is available, try:
1. The manufacturer's product page for an official product photo
2. Distributor listings (DigiKey, Mouser) which often have product images
3. Ask the user to provide an image

Whatever the source, the image must meet the same CI requirements (400x300, 4:3, under 100KB).

### Centering and cropping

**The board must be visually centered in the final image.** This means:
- The **top** and **bottom** of the board should be approximately the same distance from the
  top and bottom edges of the image
- The board should be **horizontally centered** in the frame
- There should be a comfortable amount of dead space around the board so it doesn't feel
  cropped or cramped in the UI

The source image from Adafruit CDN often has the board slightly off-center (typically shifted
upward). To correct this:

1. **View the source image** to identify the board's position. Estimate the top margin (from
   image top to board top) and bottom margin (from board bottom to image bottom).
2. Calculate the vertical shift needed: `shift = (bottom_margin - top_margin) / 2`
   - Positive shift = crop from top (board was too high)
   - Negative shift = crop from bottom (board was too low)
3. If the board is also off-center horizontally, apply a horizontal offset too.
4. After shifting, restore exact 4:3 ratio by trimming the opposite axis symmetrically.

The shift value depends on the source image resolution. If you determined a shift of `N` pixels
at one resolution, scale proportionally for a different resolution source:
`scaled_shift = N * source_height / reference_height`

```python
from PIL import Image

img = Image.open('source.jpg')
w, h = img.size

# Vertical shift to center the board (adjust based on visual inspection)
# Positive = crop from top, negative = crop from bottom
v_shift = 75  # example for a ~3000px tall source image

# Horizontal shift to center the board (usually 0, adjust if needed)
h_shift = 0   # positive = crop from left, negative = crop from right

# Apply shifts
left = max(0, h_shift)
top = max(0, v_shift)
right = w + min(0, h_shift)
bottom = h + min(0, v_shift)
cropped = img.crop((left, top, right, bottom))

# Restore exact 4:3 ratio by trimming the axis that wasn't shifted
cw, ch = cropped.size
target_w = int(ch * 4 / 3)
target_w = target_w - (target_w % 2)  # ensure even
if target_w > cw:
    # Width is limiting — trim height instead
    target_h = int(cw * 3 / 4)
    target_h = target_h - (target_h % 2)
    y_off = (ch - target_h) // 2
    final = cropped.crop((0, y_off, cw, y_off + target_h))
else:
    x_off = (cw - target_w) // 2
    final = cropped.crop((x_off, 0, x_off + target_w, ch))
```

**After processing, visually inspect the result** to confirm the board looks centered. If not,
adjust the shift values and try again. The goal is for someone glancing at the image to see the
board sitting comfortably in the middle of the frame.

### High-quality resize

Start from the **original** resolution source to minimize artifacts when downscaling. A larger
source means more pixels to sample from, producing smoother edges and finer detail.

```python
# LANCZOS is the highest quality downscale filter in PIL
resized = final.resize((400, 300), Image.LANCZOS)

# Save as high-quality JPEG (will be compressed by mozjpeg next)
resized.save('temp_highquality.jpg', 'JPEG', quality=92, subsampling=0)
```

Key points:
- Always use `Image.LANCZOS` (not `BILINEAR` or `NEAREST`) for downscaling
- Use `quality=92` and `subsampling=0` (4:4:4 chroma) for the intermediate JPEG — this
  preserves detail for mozjpeg to work with
- Starting from the `original` CDN URL (often 3000+ pixels) produces far cleaner results than
  from `640x480` or even `970x728`
- Never force-resize without cropping to 4:3 first — it stretches the image

### Compression with mozjpeg

Use **mozjpeg** (https://github.com/mozilla/mozjpeg) for best JPEG compression. The Python
package `mozjpeg-lossless-optimization` provides lossless re-encoding that typically saves
10-15% over standard JPEG encoders with no quality loss.

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
re-run mozjpeg optimization.

**Fallback** if mozjpeg is unavailable: use PIL directly with `quality=85` — this usually
produces files under 100KB for 400x300 product photos.

### Complete image processing script

```python
from PIL import Image
import urllib.request, io, os

# 1. Download original resolution source (best quality)
PID = '<product_id>'
IMAGE_NUM = '01'  # use image numbers scraped from product page
url = f'https://cdn-shop.adafruit.com/original/{PID}-{IMAGE_NUM}.jpg'
data = urllib.request.urlopen(url).read()
img = Image.open(io.BytesIO(data))
print(f'Source: {img.size}')

# 2. Center-crop (adjust shifts based on visual inspection)
w, h = img.size
v_shift = 75   # vertical: positive = crop from top (board was too high)
h_shift = 0    # horizontal: positive = crop from left
left = max(0, h_shift)
top = max(0, v_shift)
right = w + min(0, h_shift)
bottom = h + min(0, v_shift)
cropped = img.crop((left, top, right, bottom))

# 3. Restore exact 4:3 ratio
cw, ch = cropped.size
target_w = int(ch * 4 / 3)
target_w = target_w - (target_w % 2)
if target_w > cw:
    target_h = int(cw * 3 / 4)
    target_h = target_h - (target_h % 2)
    y_off = (ch - target_h) // 2
    final = cropped.crop((0, y_off, cw, y_off + target_h))
else:
    x_off = (cw - target_w) // 2
    final = cropped.crop((x_off, 0, x_off + target_w, ch))

# 4. High-quality resize from original resolution
resized = final.resize((400, 300), Image.LANCZOS)

# 5. Save intermediate high-quality JPEG
temp_path = os.path.join(os.path.dirname(__file__) or '.', 'temp_hq.jpg')
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
    print('mozjpeg not available, using PIL fallback')
    resized.save(f'components/i2c/<sensor_name>/image.jpg', 'JPEG', quality=85)

# 7. Clean up temp file
if os.path.exists(temp_path):
    os.remove(temp_path)
```

### Platform notes

- **Windows:** `/tmp` does not exist for native Python. Use `tempfile.gettempdir()` or write
  temp files to the working directory and clean up after. When using `python.exe` on Windows,
  use forward-slash paths (`C:/dev/...`) — they work in Python on Windows.
- **ImageMagick** may not be installed. Prefer Python PIL/Pillow (`pip install Pillow`).
- Always clean up intermediate files (candidates, test crops, BMPs) — only `definition.json`
  and `image.jpg` should remain in the component folder.

## Step 3 — Validate locally (if possible)

### JSON schema validation

```python
# Python validation (cross-platform, no extra tools needed)
python -c "
import json, jsonschema
schema = json.load(open('components/i2c/schema.json'))
defn = json.load(open('components/i2c/<sensor_name>/definition.json'))
jsonschema.validate(defn, schema)
print('Valid!')
"
```

```bash
# Or using ajv-cli if available
ajv validate -s components/i2c/schema.json -d "components/i2c/<sensor_name>/definition.json"
```

### Image validation

```python
# Python validation (cross-platform)
python -c "
from PIL import Image
import os
img = Image.open('components/i2c/<sensor_name>/image.jpg')
w, h = img.size
size = os.path.getsize('components/i2c/<sensor_name>/image.jpg')
ok = w == 400 and h == 300 and size < 102400
print(f'{w}x{h}, {size/1024:.1f} KB — {\"PASS\" if ok else \"FAIL\"})
"
```

### CI checks that will run on PR

1. **JSON schema validation** — definition.json against schema.json
2. **Filename validation** — only `definition.json` and `image.(png|jpg|jpeg|gif|svg)` allowed,
   all lowercase, under `components/<type>/<name>/`
3. **Image mimetype** — file extension must match actual mimetype
4. **Image dimensions** — max 400x300, must be exactly 4:3 ratio
5. **Image file size** — under 100KB
6. **URL validation** — productURL and documentationURL must be valid URIs

## Step 4 — Create PR

Branch from `main`, commit the component folder, and create a PR.

**PR title:** `Add <SENSOR> component definition`

**PR body should follow the repo template:**

```markdown
### Description
<Brief description of the sensor and what it measures>

### Product URL
<Link to product page>

### Product Documentation URL
<Link to learn guide, datasheet, or documentation>
```

If there is a companion firmware PR in `adafruit/Adafruit_Wippersnapper_Arduino`, reference it
in the description. Mention the AI model used in the PR title and body if known/available.

---

## Worked Example: TMP119

### Research
- **Duplicate check:** `components/i2c/tmp119/` does not exist, `tmp117/` does — different chip
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

`components/i2c/tmp119/image.jpg` — 400x300, board centered, mozjpeg-optimized, 45KB.

### Image processing
- Source: `https://cdn-shop.adafruit.com/original/6482-01.jpg` (3974x3056)
- Board was slightly high in frame — applied v_shift=75 to center vertically
- Resized with LANCZOS from original resolution for clean downscale
- Compressed with mozjpeg lossless optimization

### Companion PR
Firmware driver PR in `adafruit/Adafruit_Wippersnapper_Arduino` — references this components PR.

---

## Worked Example: D6T-1A (Non-Adafruit Product)

### Research
- **No Adafruit product page** — this is an OMRON sensor sold via DigiKey
- Vendor: OMRON
- I2C address: 0x0A (fixed, no ADDR pin)
- Measures: Ambient temp + object temp (non-contact thermal IR)
- Uses object-style subcomponents because it has both ambient and object temperature readings
  and the display names add clarity

### Files created

`components/i2c/d6t1a/definition.json`:
```json
{
  "displayName": "D6T-1A Thermal Sensor",
  "description": "Non-contact MEMS thermal infrared sensor for precise temperature detection (D6T-1A-01/D6T-1A-02)",
  "vendor": "OMRON",
  "productURL": "https://www.digikey.com/en/products/detail/omron-electronics-inc-emc-div/D6T-1A-02/8602566",
  "documentationURL": "https://omronfs.omron.com/en_US/ecb/products/pdf/en_D6T_users_manual.pdf",
  "published": true,
  "i2cAddresses": ["0x0A"],
  "subcomponents": [
    {"displayName": "Ambient Temperature (°C)", "sensorType": "ambient-temp"},
    {"displayName": "Ambient Temperature (°F)", "sensorType": "ambient-temp-fahrenheit"},
    {"displayName": "Measured Object Temp (°C)", "sensorType": "object-temp"},
    {"displayName": "Measured Object Temp (°F)", "sensorType": "object-temp-fahrenheit"}
  ]
}
```

Note: Image sourced from manufacturer product page since no Adafruit CDN image exists.
Third-party documentation URLs may need domain allowlisting by a maintainer before CI passes.
