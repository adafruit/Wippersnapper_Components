# Wippersnapper Component Definitions

Welcome! This repository contains JSON definition files and images that allow components and sensors to be used with [WipperSnapper](https://io.adafruit.com/wippersnapper), Adafruit IO's no-code IoT service.

[Click here to submit suggestions or requests for new Adafruit.io WipperSnapper components >>>](https://github.com/adafruit/WipperSnapper_Components/issues/new/choose)

## How Does It Work?

Anyone can add a new component to WipperSnapper by writing a small amount of descriptive JSON and adding an image! If accepted, a supported component will:

### Appear in this list

<img width="611" alt="Screen Shot 2021-12-02 at 4 28 11 PM" src="https://user-images.githubusercontent.com/17697/144505905-1d1a34e0-df2b-4ee2-9dd2-309e389d14d5.png">

### Generate a custom web form, like this

<img width="611" alt="Screen Shot 2021-12-02 at 4 28 40 PM" src="https://user-images.githubusercontent.com/17697/144505968-e5a1385a-f73e-4948-8380-fee40945a38f.png">

### Have its current status tracked or controlled, like this

<img width="743" alt="Screen Shot 2021-12-02 at 4 29 31 PM" src="https://user-images.githubusercontent.com/17697/144506091-971ef397-63d2-4477-9ea8-b645896af07f.png">

### And work seamlessly with the rest of IO, including Dashboards and Triggers!

## Repository Structure

```
components/
├── pin/             # Digital/analog GPIO components (LEDs, buttons, sensors)
├── i2c/             # I2C sensor breakouts (temperature, pressure, humidity, etc.)
├── i2c_output/      # I2C output displays (OLEDs, character LCDs, 7-segment, etc.)
├── display/         # TFT and E-Ink displays
├── ds18x20/         # 1-Wire DS18x20 temperature sensors
├── pixel/           # Addressable LED strips (NeoPixel, DotStar)
├── pwm/             # PWM-controlled devices (dimmable LEDs, buzzers)
├── servo/           # Servo motor controllers
├── uart/            # UART/serial-based sensors
└── sensors.json     # Centralized registry of sensor type definitions
```

Each component type directory contains:
- `schema.json` — JSON Schema that validates component definitions of that type.
- One subdirectory per component, each containing:
  - `definition.json` — The component definition (validated against the parent `schema.json`).
  - An image file (`.jpg`, `.png`, or `.svg`) — A photo or illustration of the component.

## Contributing

[We have a guide on adding components to this repository on the Adafruit Learning System](https://learn.adafruit.com/how-to-add-a-new-component-to-adafruit-io-wippersnapper)

### Image Requirements

Images included with component definitions are validated by CI and must meet these requirements:

| Requirement      | Rule                                                    |
|------------------|---------------------------------------------------------|
| **Format**       | `.jpg`, `.png`, or `.svg` only                          |
| **Aspect Ratio** | 4:3                                                     |
| **Max Size**     | 400 × 300 pixels                                        |
| **Max File Size**| 100 KB                                                  |
| **Filename**     | Lowercase only, no uppercase characters                 |

### Validation

All pull requests are automatically validated by CI. The validation pipeline checks:

1. **Schema validation** — Every `definition.json` is validated against its component type's `schema.json`.
2. **Filename rules** — No uppercase characters in file or directory names.
3. **Image rules** — Format, dimensions, aspect ratio, and file size (see above).
4. **Permission scoping** — External contributors may only add or modify `definition.json` files and images within component subdirectories. Only Adafruit maintainers may modify `schema.json` or `sensors.json`.

## Common Fields

All component types share these optional fields (in addition to their type-specific required fields):

| Field              | Type    | Description                                                                                                         |
|--------------------|---------|---------------------------------------------------------------------------------------------------------------------|
| `displayName`      | string  | **(required)** The human-friendly name of this component. 3–24 characters (up to 30 for some types).               |
| `vendor`           | string  | **(required)** Name of the company that makes this component. 3–24 characters.                                      |
| `published`        | boolean | If `true`, the component is visible to all users. If `false`, it is hidden behind a developer toggle.               |
| `description`      | string  | A brief description of the component's capabilities. 3–255 characters.                                              |
| `productURL`       | string  | URL to the component's product page.                                                                                |
| `documentationURL` | string  | URL to the component's documentation.                                                                               |

## Component Formats

### Pin

Pin components represent digital or analog GPIO-based devices such as LEDs, buttons, and basic sensors.

**Required fields:** `displayName`, `vendor`, `mode`, `direction`

| Field             | Type    | Description                                                                                              |
|-------------------|---------|----------------------------------------------------------------------------------------------------------|
| `mode`            | string  | `DIGITAL` or `ANALOG`.                                                                                   |
| `direction`       | string  | `INPUT` or `OUTPUT`.                                                                                     |
| `autoSelectString`| string  | A hint for automatically looking up appropriate pin names. 3–24 characters.                              |
| `selectPullUp`    | boolean | If `true`, the user can select pull-up or pull-down options.                                             |
| `pull`            | string  | Pull setting: `UP` or `DOWN`.                                                                            |
| `selectReadMode`  | boolean | If `true`, the user can select the read mode between pin value and voltage.                              |
| `analogReadMode`  | string  | For `ANALOG` mode: `PIN_VALUE` or `PIN_VOLTAGE`. Acts as a default if `selectReadMode` is `true`.       |
| `defaultPeriod`   | number  | Default polling period in seconds (30–86400). If present, the form allows the user to set a period.      |
| `forceOnPeriod`   | boolean | If `true`, the user must specify a period (it won't be optional).                                        |
| `visualization`   | object  | Specifies the UI widget. See [Pin Visualization Types](#pin-visualization-types).                        |

#### Pin Visualization Types

The `visualization` object requires a `type` field. Allowed types:

- **`switch`** — A toggle switch. Optional fields: `offLabel`, `offIcon`, `onLabel`, `onIcon` (strings).
- **`button`** — A push button. Optional fields: `pressedLabel`, `unpressedLabel` (strings).
- **`slider`** — A slider control. No additional fields.

<details>
<summary>Example: LED (digital output with switch)</summary>

```json
{
  "displayName": "LED",
  "vendor": "Generic",
  "mode": "DIGITAL",
  "direction": "OUTPUT",
  "visualization": {
    "type": "switch",
    "offIcon": "fa6:regular:lightbulb",
    "onIcon": "fa6:solid:lightbulb-on"
  }
}
```
</details>

<details>
<summary>Example: Push Button (digital input)</summary>

```json
{
  "displayName": "Push Button",
  "vendor": "Generic",
  "mode": "DIGITAL",
  "direction": "INPUT",
  "defaultPeriod": 30,
  "selectPullUp": true,
  "visualization": {
    "type": "button"
  }
}
```
</details>

<details>
<summary>Example: Potentiometer (analog input with slider)</summary>

```json
{
  "displayName": "Potentiometer",
  "vendor": "Generic",
  "mode": "ANALOG",
  "direction": "INPUT",
  "defaultPeriod": 60,
  "analogReadMode": "PIN_VALUE",
  "selectReadMode": true,
  "visualization": {
    "type": "slider"
  }
}
```
</details>

---

### I2C

I2C components represent sensor breakouts connected via the I2C bus. Each component can expose one or more sensor subcomponents (e.g. a BME280 exposes temperature, humidity, pressure, and altitude).

**Required fields:** `displayName`, `vendor`, `i2cAddresses`, `subcomponents`

| Field            | Type   | Description                                                                                            |
|------------------|--------|--------------------------------------------------------------------------------------------------------|
| `i2cAddresses`   | array  | List of I2C addresses as hex strings (e.g. `"0x77"`).                                                  |
| `subcomponents`  | array  | List of sensor subcomponents. Each item is either a sensor type string or an object (see below).       |

#### I2C Subcomponent Format

Each subcomponent can be a simple sensor type string (e.g. `"ambient-temp"`) or an object with:

| Field          | Type   | Description                                                                    |
|----------------|--------|--------------------------------------------------------------------------------|
| `displayName`  | string | **(required)** Human-friendly name of the sensor. 3–30 characters.             |
| `sensorType`   | string | **(required)** One of the supported [Sensor Types](#sensor-types).             |
| `type`         | string | A unique lookup string for this sensor and its parent component. 3–24 chars.   |
| `defaultPeriod`| number | Default polling period in seconds (1–86400).                                   |

<details>
<summary>Example: BME280 (multiple subcomponents)</summary>

```json
{
  "displayName": "BME280",
  "vendor": "Bosch",
  "i2cAddresses": ["0x77", "0x76"],
  "subcomponents": [
    "ambient-temp",
    "ambient-temp-fahrenheit",
    "humidity",
    "pressure",
    "altitude"
  ]
}
```
</details>

<details>
<summary>Example: SEN55 (subcomponents as objects with custom display names)</summary>

```json
{
  "displayName": "SEN55",
  "vendor": "Sensirion",
  "i2cAddresses": ["0x69"],
  "subcomponents": [
    { "displayName": "Temperature", "sensorType": "ambient-temp" },
    { "displayName": "Temperature (°F)", "sensorType": "ambient-temp-fahrenheit" },
    { "displayName": "Humidity", "sensorType": "humidity" },
    { "displayName": "PM1.0 Std", "sensorType": "pm10-std" },
    { "displayName": "PM2.5 Std", "sensorType": "pm25-std" },
    { "displayName": "PM10.0 Std", "sensorType": "pm100-std" },
    { "displayName": "VOC Index", "sensorType": "voc-index" },
    { "displayName": "NOx Index", "sensorType": "nox-index" }
  ]
}
```
</details>

---

### I2C Output

I2C output components represent I2C-connected display devices such as character LCDs, 7-segment displays, alphanumeric displays, and OLEDs.

**Required fields:** `displayName`, `vendor`, `outputType`, `i2cAddresses`

| Field                  | Type   | Description                                                                         |
|------------------------|--------|-------------------------------------------------------------------------------------|
| `i2cAddresses`         | array  | List of supported I2C addresses as hex strings (e.g. `"0x3C"`).                    |
| `outputType`           | string | One of: `CHARLCD`, `7SEG`, `ALPHANUM`, `OLED`.                                     |
| `charLcdColumns`       | number | Number of columns for a character LCD (0–128). Used when `outputType` is `CHARLCD`. |
| `charLcdRows`          | number | Number of rows for a character LCD (0–16). Used when `outputType` is `CHARLCD`.     |
| `ledBackpackAlignment` | string | Text alignment for LED backpack displays: `LEFT` or `RIGHT`.                        |
| `ledBackpackBrightness`| number | Brightness level for LED backpack displays (0–15).                                  |
| `oledWidth`            | number | OLED display width in pixels (0–128). Used when `outputType` is `OLED`.             |
| `oledHeight`           | number | OLED display height in pixels (0–64). Used when `outputType` is `OLED`.             |
| `textSize`             | string | Font size for OLED displays: `SZ_DEFAULT` or `SZ_LARGE`.                            |

<details>
<summary>Example: 16x2 Character LCD</summary>

```json
{
  "displayName": "16x2 Character Display",
  "vendor": "Adafruit",
  "i2cAddresses": ["0x20", "0x21", "0x22", "0x23", "0x24", "0x25", "0x26", "0x27"],
  "outputType": "CHARLCD",
  "charLcdColumns": 16,
  "charLcdRows": 2
}
```
</details>

<details>
<summary>Example: 128x64 OLED</summary>

```json
{
  "displayName": "128x64 OLED (Default)",
  "vendor": "Adafruit",
  "i2cAddresses": ["0x3D", "0x3C"],
  "outputType": "OLED",
  "oledWidth": 128,
  "oledHeight": 64,
  "textSize": "SZ_DEFAULT"
}
```
</details>

---

### Display

Display components represent SPI-connected TFT and E-Ink (EPD) screens.

**Required fields:** `displayName`, `vendor`, `displayType`

The `displayType` object is required and contains:

| Field       | Type   | Description                                                                    |
|-------------|--------|--------------------------------------------------------------------------------|
| `type`      | string | **(required)** Display type: `epd` (E-Paper) or `tft`.                        |
| `driver`    | string | **(required)** Driver: `epd_ssd1680`, `epd_ili0373`, `tft_st7789`, or `unspecified`. |
| `spiEpd`    | object | Optional SPI bus config for EPD displays (`{ "bus": <int> }`).                 |
| `spiTft`    | object | Optional SPI bus config for TFT displays (`{ "bus": <int> }`).                 |
| `epdConfig` | object | EPD-specific config (see below). Required fields: `mode`, `width`, `height`, `textSize`. |
| `tftConfig` | object | TFT-specific config (see below). Required fields: `width`, `height`, `rotation`, `textSize`. |

**EPD config (`epdConfig`):**

| Field      | Type    | Description                                             |
|------------|---------|---------------------------------------------------------|
| `mode`     | string  | Display mode: `unspecified`, `grayscale4`, or `mono`.   |
| `width`    | integer | Display width in pixels (min 1).                        |
| `height`   | integer | Display height in pixels (min 1).                       |
| `textSize` | integer | Default text size (scale of 8×5 px font).               |

**TFT config (`tftConfig`):**

| Field      | Type    | Description                                             |
|------------|---------|---------------------------------------------------------|
| `width`    | integer | Display width in pixels (min 1).                        |
| `height`   | integer | Display height in pixels (min 1).                       |
| `rotation` | integer | Screen rotation: `0`, `1`, `2`, or `3`.                 |
| `textSize` | integer | Default text size (scale of 8×5 px font).               |

<details>
<summary>Example: TFT display</summary>

```json
{
  "displayName": "1.14\" TFT LCD Display",
  "vendor": "Adafruit",
  "description": "Adafruit 1.14\" 240x135 Color TFT Display + MicroSD Card Breakout - ST7789",
  "displayType": {
    "type": "tft",
    "driver": "tft_st7789",
    "tftConfig": {
      "height": 240,
      "width": 135,
      "rotation": 3,
      "textSize": 3
    }
  }
}
```
</details>

<details>
<summary>Example: E-Ink display</summary>

```json
{
  "displayName": "MagTag - 2.9\" E-Ink Display",
  "vendor": "Adafruit",
  "description": "Adds the Adafruit MagTag's 2.9\" E-Ink display to your project.",
  "displayType": {
    "type": "epd",
    "driver": "unspecified",
    "epdConfig": {
      "mode": "unspecified",
      "width": 296,
      "height": 128,
      "textSize": 3
    }
  }
}
```
</details>

---

### DS18x20

DS18x20 components represent 1-Wire temperature sensors (e.g. DS18B20).

**Required fields:** `displayName`, `vendor`, `subcomponents`, `sensorResolution`

| Field              | Type   | Description                                                                        |
|--------------------|--------|------------------------------------------------------------------------------------|
| `subcomponents`    | array  | Temperature sensor types. Each item must match `ambient-temp` or `ambient-temp-fahrenheit`. |
| `sensorResolution` | number | Desired sensor read resolution in bits (9–12).                                     |

<details>
<summary>Example: DS18B20</summary>

```json
{
  "displayName": "DS18B20",
  "vendor": "Generic",
  "subcomponents": ["ambient-temp", "ambient-temp-fahrenheit"],
  "sensorResolution": 12
}
```
</details>

---

### Pixel

Pixel components represent addressable LED strips such as NeoPixels and DotStars.

**Required fields:** `displayName`, `vendor`, `pixelsType`, `defaultPixelsOrder`

| Field               | Type   | Description                                                        |
|---------------------|--------|--------------------------------------------------------------------|
| `pixelsType`        | string | `NEOPIXEL` or `DOTSTAR`.                                          |
| `defaultPixelsOrder`| string | Color ordering: `GRB`, `GRBW`, `RGB`, `RGBW`, or `BRG`.          |
| `autoSelectString`  | string | Pin name hint for automatic selection. 3–24 characters.            |

<details>
<summary>Example: NeoPixel</summary>

```json
{
  "displayName": "NeoPixel",
  "vendor": "Generic",
  "pixelsType": "NEOPIXEL",
  "defaultPixelsOrder": "GRB",
  "autoSelectString": "neopixel"
}
```
</details>

---

### PWM

PWM components represent pulse-width-modulation-controlled devices such as dimmable LEDs, RGB LEDs, and buzzers.

**Required fields:** `displayName`, `vendor`, `pwmSetting`

| Field           | Type   | Description                                                                                     |
|-----------------|--------|-------------------------------------------------------------------------------------------------|
| `pwmSetting`    | string | `fixedFrequency` (variable duty cycle) or `variableFrequency` (fixed duty cycle).               |
| `visualization` | object | Specifies the UI widget. See [PWM Visualization Types](#pwm-visualization-types).               |

#### PWM Visualization Types

- **`switch-pwm`** — A toggle switch. Optional fields: `offLabel`, `offIcon`, `onLabel`, `onIcon`.
- **`button`** — A push button. Optional fields: `pressedLabel`, `unpressedLabel`.
- **`slider-pwm`** — A slider control. No additional fields.
- **`color-picker`** — A color picker (used for RGB LEDs). No additional fields.

<details>
<summary>Example: Dimmable LED</summary>

```json
{
  "displayName": "Dimmable LED",
  "vendor": "Generic",
  "pwmSetting": "fixedFrequency",
  "visualization": {
    "type": "slider-pwm"
  }
}
```
</details>

<details>
<summary>Example: RGB LED</summary>

```json
{
  "displayName": "RGB LED",
  "vendor": "Generic",
  "pwmSetting": "fixedFrequency",
  "visualization": {
    "type": "color-picker"
  }
}
```
</details>

---

### Servo

Servo components represent servo motor controllers.

**Required fields:** `displayName`, `vendor`, `frequency`, `minPulseWidth`, `maxPulseWidth`

| Field           | Type   | Description                                                           |
|-----------------|--------|-----------------------------------------------------------------------|
| `frequency`     | number | PWM signal frequency in Hz (40–200, default 50).                      |
| `minPulseWidth` | number | Minimum pulse width in microseconds.                                  |
| `maxPulseWidth` | number | Maximum pulse width in microseconds.                                  |
| `visualization` | object | Only supported type: `slider-servo`.                                  |

<details>
<summary>Example: Generic Servo</summary>

```json
{
  "displayName": "Generic Servo",
  "vendor": "Generic",
  "frequency": 50,
  "minPulseWidth": 500,
  "maxPulseWidth": 2500,
  "visualization": {
    "type": "slider-servo"
  }
}
```
</details>

---

### UART

UART components represent serial-bus-based sensors such as air quality monitors.

**Required fields:** `displayName`, `vendor`, `subcomponents`, `baudRate`

| Field           | Type    | Description                                                                           |
|-----------------|---------|---------------------------------------------------------------------------------------|
| `subcomponents` | array   | List of sensor subcomponents (same format as [I2C subcomponents](#i2c-subcomponent-format)). |
| `baudRate`      | number  | UART baud rate in bps (1200–256000).                                                  |
| `inverted`      | boolean | If `true`, TX/RX signals on the UART bus are inverted.                                |

<details>
<summary>Example: PMS5003 Air Quality Sensor</summary>

```json
{
  "displayName": "pms5003",
  "vendor": "PLANTOWER",
  "subcomponents": [
    "pm10-std",
    "pm25-std",
    "pm100-std",
    "pm10-env",
    "pm25-env",
    "pm100-env"
  ],
  "baudRate": 9600,
  "inverted": false
}
```
</details>

## Sensor Types

Sensor type strings are used in the `subcomponents` arrays of I2C, UART, and DS18x20 components. The centralized registry of display names and default polling periods is in [`components/sensors.json`](components/sensors.json).

| Sensor Type                | Display Name                             |
|----------------------------|------------------------------------------|
| `unspecified`              | Unspecified                              |
| `accelerometer`            | Accelerometer                            |
| `magnetic-field`           | Magnetic Field Sensor                    |
| `orientation`              | Orientation Sensor                       |
| `gyroscope`                | Gyroscope                                |
| `light`                    | Light Sensor                             |
| `pressure`                 | Pressure Sensor                          |
| `proximity`                | Proximity Sensor                         |
| `gravity`                  | Gravity Sensor                           |
| `acceleration`             | Acceleration Sensor                      |
| `rotation`                 | Rotation Sensor                          |
| `humidity`                 | Humidity Sensor                          |
| `ambient-temp`             | Temperature Sensor (°C)                  |
| `ambient-temp-fahrenheit`  | Temperature Sensor (°F)                  |
| `object-temp`              | Thermocouple Temperature (°C)            |
| `object-temp-fahrenheit`   | Thermocouple Temperature (°F)            |
| `voltage`                  | Voltage Sensor                           |
| `current`                  | Current                                  |
| `color`                    | Color                                    |
| `raw`                      | Raw Data                                 |
| `pm10-std`                 | PM1.0 Standard                           |
| `pm25-std`                 | PM2.5 Standard                           |
| `pm100-std`                | PM10.0 Standard                          |
| `pm10-env`                 | PM1.0 Environmental                      |
| `pm25-env`                 | PM2.5 Environmental                      |
| `pm100-env`                | PM10.0 Environmental                     |
| `co2`                      | CO2                                      |
| `gas-resistance`           | Total VOC                                |
| `altitude`                 | Altitude (Relative)                      |
| `lux`                      | Light Sensor (Lux)                       |
| `eco2`                     | Estimated CO2                            |
| `unitless-percent`         | Unitless Percentage                      |
| `voc-index`                | Volatile Organic Compounds Index         |
| `nox-index`                | Nitrogen Oxides Index                    |
| `tvoc`                     | Total Volatile Organic Compounds         |
