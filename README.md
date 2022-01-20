# Wippersnapper Component Definitions

Welcome! This repository contains JSON definition files and images that allow components and sensors to be used with WipperSnapper, Adafruit IO's no-code IoT service.

**Note: We aren't ready for user submissions yet as we're still finalizing the format and tools.**

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

## Pin Component Format
todo

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
- "object-temp"
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
