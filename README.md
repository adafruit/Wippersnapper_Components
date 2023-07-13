# Wippersnapper Component Definitions

Welcome! This repository contains JSON definition files and images that allow components and sensors to be used with WipperSnapper, Adafruit IO's no-code IoT service.

[Click here to submit suggestions or requests for new Adafruit.io WipperSnapper components >>>](https://github.com/adafruit/WipperSnapper_Components/issues/new/choose)


## How Will It Work?

Anyone can add a new component to Wippersnapper by writing a small amount of descriptive JSON and adding an image! If accepted, a supported component will:

### Appear in this list

<img width="611" alt="Screen Shot 2021-12-02 at 4 28 11 PM" src="https://user-images.githubusercontent.com/17697/144505905-1d1a34e0-df2b-4ee2-9dd2-309e389d14d5.png">

### Generate a custom web form, like this

<img width="611" alt="Screen Shot 2021-12-02 at 4 28 40 PM" src="https://user-images.githubusercontent.com/17697/144505968-e5a1385a-f73e-4948-8380-fee40945a38f.png">

### Have its current status tracked or controlled, like this

<img width="743" alt="Screen Shot 2021-12-02 at 4 29 31 PM" src="https://user-images.githubusercontent.com/17697/144506091-971ef397-63d2-4477-9ea8-b645896af07f.png">

### And work seamlessly with the rest of IO, including Dashboards and Triggers!

## Contributing

[We have a guide on adding components to this repository on the Adafruit Learning System](https://learn.adafruit.com/how-to-add-a-new-component-to-adafruit-io-wippersnapper)

## Pin Component Format

|                                      |                                                                                                                                                           |
|--------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| *title*                              | Pin Component Definition                                                                                                                                  |
| *description*                        | A pin-based WipperSnapper component for use in Adafruit IO                                                                                                |
| *type*                               | object                                                                                                                                                    |
| *required*                           |                                                                                                                                                           |
| displayName                          |                                                                                                                                                           |
| mode                                 |                                                                                                                                                           |
| direction                            |                                                                                                                                                           |
| *additionalProperties*               | false                                                                                                                                                     |
| *properties*                         |                                                                                                                                                           |
| `displayName`                        |                                                                                                                                                           |
| *description*                        | The human-friendly name of this component.                                                                                                                |
| *type*                               | string                                                                                                                                                    |
| *minLength*                          | 3                                                                                                                                                         |
| *maxLength*                          | 24                                                                                                                                                        |
| `mode`                               |                                                                                                                                                           |
| *description*                        | This component's mode, either DIGITAL or ANALOG.                                                                                                          |
| *type*                               | string                                                                                                                                                    |
| *pattern*                            | `^(DIGITAL\|ANALOG)$`                                                                                                                                     |
| `direction`                          |                                                                                                                                                           |
| *description*                        | This component's direction, either INPUT or OUTPUT.                                                                                                       |
| *type*                               | string                                                                                                                                                    |
| *pattern*                            | `^(INPUT\|OUTPUT)$`                                                                                                                                       |
| `autoSelectString`                   |                                                                                                                                                           |
| *description*                        | A hint for automatically looking up pin names that may be appropriate for this kind of component.                                                         |
| *type*                               | string                                                                                                                                                    |
| *minLength*                          | 3                                                                                                                                                         |
| *maxLength*                          | 24                                                                                                                                                        |
| `selectPullUp`                       |                                                                                                                                                           |
| *description*                        | If true, the user will be able to select pull up or down options.                                                                                         |
| *type*                               | boolean                                                                                                                                                   |
| `pull`                               |                                                                                                                                                           |
| *description*                        | This component's pull setting, either UP or DOWN.                                                                                                         |
| *type*                               | string                                                                                                                                                    |
| *pattern*                            | `^(UP\|DOWN)$`                                                                                                                                            |
| `selectReadMode`                     |                                                                                                                                                           |
| *description*                        | If true, the user will be able to select the read mode between pin and voltage options.                                                                   |
| *type*                               | boolean                                                                                                                                                   |
| `analogReadMode`                     |                                                                                                                                                           |
| *description*                        | For ANALOG mode components, specifies whether to read values (PIN_VALUE) or voltages (PIN_VOLTAGE). Will be a default if `selectReadMode` option is true. |
| *type*                               | string                                                                                                                                                    |
| *pattern*                            | `^(PIN_VALUE\|PIN_VOLTAGE)$`                                                                                                                              |
| `defaultPeriod`                      |                                                                                                                                                           |
| *description*                        | If present, the component form will allow the user to set its period, with this value as the default (in seconds)                                         |
| *type*                               | number                                                                                                                                                    |
| *minimum*                            | 30                                                                                                                                                        |
| *maximum*                            | 86400                                                                                                                                                     |
| `forceOnPeriod`                      |                                                                                                                                                           |
| *description*                        | If true, the user must specify a period (won't be optional in the form).                                                                                  |
| *type*                               | boolean                                                                                                                                                   |
| `visualization`                      |                                                                                                                                                           |
| *description*                        | Specifies which visual component to use in the WipperSnapper interface and how to configure it                                                            |
| *type*                               | object                                                                                                                                                    |
| `discriminator`                      |                                                                                                                                                           |
| *propertyName*                       | type                                                                                                                                                      |
| *required*                           |                                                                                                                                                           |
| type                                 |                                                                                                                                                           |
| *oneOf*                              |                                                                                                                                                           |

| *properties*     |                   | *additionalProperties*                                                                                                                                    |
|------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| type             |                   |                                                                                                                                                           |
| const            | switch            | false                                                                                                                                                     |
| `offLabel`       |                   | false                                                                                                                                                     |
| type             | string            | false                                                                                                                                                     |
| `offIcon`        |                   | false                                                                                                                                                     |
| type             | string            | false                                                                                                                                                     |
| `onLabel`        |                   | false                                                                                                                                                     |
| type             | string            | false                                                                                                                                                     |
| `onIcon`         |                   | false                                                                                                                                                     |
| type             | string            | false                                                                                                                                                     |
| type             |                   | false                                                                                                                                                     |

| *properties*     |                   | *additionalProperties*                                                                                                                                    |
|------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| type             |                   | false                                                                                                                                                     |
| const            | button            | false                                                                                                                                                     |
| `pressedLabel`   |                   | false                                                                                                                                                     |
| type             | string            | false                                                                                                                                                     |
| `unpressedLabel` |                   | false                                                                                                                                                     |
| type             | string            | false                                                                                                                                                     |

| *properties*     |                   | *additionalProperties*                                                                                                                                    |
|------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| type             |                   | false                                                                                                                                                     |
| const            | slider            | false                                                                                                                                                     |

## I2C Component Format
todo

## Legend

## I2C Sensor Types

Possible values for an I2C component's subcomponents' `sensorType` field:

- "unspecified"
- "accelerometer"
- "magnetic-field"
- "orientation"
- "gyroscope"
- "light"
- "pressure"
- "proximity"
- "gravity"
- "acceleration"
- "rotation"
- "humidity"
- "ambient-temp"
- "ambient-temp-fahrenheit"
- "object-temp"
- "object-temp-fahrenheit"
- "voltage"
- "current"
- "color"
- "raw"
- "pm10-std"
- "pm25-std"
- "pm100-std"
- "pm10-env"
- "pm25-env"
- "pm100-env"
- "co2"
- "gas-resistance"
- "altitude"
- "lux"
- "eco2"
- "unitless-percent"
- "tvoc"
