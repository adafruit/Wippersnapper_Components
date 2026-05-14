# Settings Index-to-Enum Differences

When a setting's option index maps directly to the hardware enum value, firmware can pass
the index straight through. When it doesn't, firmware needs a lookup table.

This file tracks settings where the **JSON option index != hardware enum value**.

## BMP3xx — Output Data Rate

The full BMP3 ODR enum has 18 values (0x00–0x11). The JSON options are trimmed to a
practical subset, so the index no longer matches the enum.

| JSON Index | Option Label | BMP3 Enum Value | Enum Name |
|------------|-------------|-----------------|-----------|
| 0 | 50 Hz | 0x02 | BMP3_ODR_50_HZ |
| 1 | 25 Hz | 0x03 | BMP3_ODR_25_HZ |
| 2 | 12.5 Hz | 0x04 | BMP3_ODR_12_5_HZ |
| 3 | 6.25 Hz | 0x05 | BMP3_ODR_6_25_HZ |
| 4 | 3.1 Hz | 0x06 | BMP3_ODR_3_1_HZ |
| 5 | 1.5 Hz | 0x07 | BMP3_ODR_1_5_HZ |
| 6 | 0.78 Hz | 0x08 | BMP3_ODR_0_78_HZ |
| 7 | 0.39 Hz | 0x09 | BMP3_ODR_0_39_HZ |
| 8 | 0.2 Hz | 0x0A | BMP3_ODR_0_2_HZ |

## APDS9999 — LED Current

The LED current enum starts at 0x02.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | 10mA | 0x02 | APDS9999_LED_CURRENT_10MA |
| 1 | 25mA | 0x03 | APDS9999_LED_CURRENT_25MA |

## APDS9999 — LED Frequency

The LED frequency enum starts at 0x03.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | 60 kHz | 0x03 | APDS9999_LED_FREQ_60KHZ |
| 1 | 70 kHz | 0x04 | APDS9999_LED_FREQ_70KHZ |
| 2 | 80 kHz | 0x05 | APDS9999_LED_FREQ_80KHZ |
| 3 | 90 kHz | 0x06 | APDS9999_LED_FREQ_90KHZ |
| 4 | 100 kHz | 0x07 | APDS9999_LED_FREQ_100KHZ |

## APDS9999 — Proximity Measurement Rate

The proximity measurement rate enum starts at 0x01.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | 6.25ms | 0x01 | APDS9999_PROX_RATE_6MS |
| 1 | 12.5ms | 0x02 | APDS9999_PROX_RATE_12MS |
| 2 | 25ms | 0x03 | APDS9999_PROX_RATE_25MS |
| 3 | 50ms | 0x04 | APDS9999_PROX_RATE_50MS |
| 4 | 100ms | 0x05 | APDS9999_PROX_RATE_100MS |
| 5 | 200ms | 0x06 | APDS9999_PROX_RATE_200MS |
| 6 | 400ms | 0x07 | APDS9999_PROX_RATE_400MS |

## MAX44009 — Mode

The MAX44009 mode enum uses non-contiguous bit-flag values.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | Default (Auto, 800ms cycle) | 0x00 | MAX44009_MODE_DEFAULT |
| 1 | Continuous (Auto, fast) | 0x80 | MAX44009_MODE_CONTINUOUS |
| 2 | Manual (800ms cycle) | 0x40 | MAX44009_MODE_MANUAL |
| 3 | Manual Continuous (fast) | 0xC0 | MAX44009_MODE_MANUAL_CONTINUOUS |

## LTR329/303 — Gain

The LTR3XX gain enum skips values 4 and 5. Options are presented without the gap.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | 1x | 0 | LTR3XX_GAIN_1 |
| 1 | 2x | 1 | LTR3XX_GAIN_2 |
| 2 | 4x | 2 | LTR3XX_GAIN_4 |
| 3 | 8x | 3 | LTR3XX_GAIN_8 |
| 4 | 48x | 6 | LTR3XX_GAIN_48 |
| 5 | 96x | 7 | LTR3XX_GAIN_96 |

## LTR329/303 — Integration Time

The LTR3XX integration time enum is in a non-logical order. Options are presented
in ascending order.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | 50ms | 1 | LTR3XX_INTEGTIME_50 |
| 1 | 100ms | 0 | LTR3XX_INTEGTIME_100 |
| 2 | 150ms | 4 | LTR3XX_INTEGTIME_150 |
| 3 | 200ms | 2 | LTR3XX_INTEGTIME_200 |
| 4 | 250ms | 5 | LTR3XX_INTEGTIME_250 |
| 5 | 300ms | 6 | LTR3XX_INTEGTIME_300 |
| 6 | 350ms | 7 | LTR3XX_INTEGTIME_350 |
| 7 | 400ms | 3 | LTR3XX_INTEGTIME_400 |

## VEML7700 — Integration Time

The VEML7700 integration time defines have non-contiguous values — 50ms and 25ms
jump to 0x08 and 0x0C respectively.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | 25ms | 0x0C | VEML7700_IT_25MS |
| 1 | 50ms | 0x08 | VEML7700_IT_50MS |
| 2 | 100ms | 0x00 | VEML7700_IT_100MS |
| 3 | 200ms | 0x01 | VEML7700_IT_200MS |
| 4 | 400ms | 0x02 | VEML7700_IT_400MS |
| 5 | 800ms | 0x03 | VEML7700_IT_800MS |

## BMP5xx — Output Data Rate

The full BMP5 ODR enum has 32 values (0x00–0x1F). The JSON options are trimmed to a
practical subset, so the index no longer matches the enum.

| JSON Index | Option Label | BMP5 Enum Value | Enum Name |
|------------|-------------|-----------------|-----------|
| 0 | 50 Hz | 0x0F | BMP5_ODR_50_HZ |
| 1 | 45 Hz | 0x10 | BMP5_ODR_45_HZ |
| 2 | 40 Hz | 0x11 | BMP5_ODR_40_HZ |
| 3 | 35 Hz | 0x12 | BMP5_ODR_35_HZ |
| 4 | 30 Hz | 0x13 | BMP5_ODR_30_HZ |
| 5 | 25 Hz | 0x14 | BMP5_ODR_25_HZ |
| 6 | 20 Hz | 0x15 | BMP5_ODR_20_HZ |
| 7 | 15 Hz | 0x16 | BMP5_ODR_15_HZ |
| 8 | 10 Hz | 0x17 | BMP5_ODR_10_HZ |
| 9 | 5 Hz | 0x18 | BMP5_ODR_05_HZ |
| 10 | 4 Hz | 0x19 | BMP5_ODR_04_HZ |
| 11 | 3 Hz | 0x1A | BMP5_ODR_03_HZ |
| 12 | 2 Hz | 0x1B | BMP5_ODR_02_HZ |
| 13 | 1 Hz | 0x1C | BMP5_ODR_01_HZ |
| 14 | 0.5 Hz | 0x1D | BMP5_ODR_0_5_HZ |
| 15 | 0.25 Hz | 0x1E | BMP5_ODR_0_250_HZ |
| 16 | 0.125 Hz | 0x1F | BMP5_ODR_0_125_HZ |

## BMP280 — Sensor Mode

The BMP280 mode enum skips 0x02 and includes a soft reset code (0xB6) that is not exposed.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | Sleep | 0x00 | MODE_SLEEP |
| 1 | Forced | 0x01 | MODE_FORCED |
| 2 | Normal | 0x03 | MODE_NORMAL |

## TMP117/TMP119 — Measurement Mode

The TMP117 mode enum has a gap — value 0x02 is a duplicate of CONTINUOUS and is skipped.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | Continuous | 0x00 | TMP117_MODE_CONTINUOUS |
| 1 | Shutdown | 0x01 | TMP117_MODE_SHUTDOWN |
| 2 | One Shot | 0x03 | TMP117_MODE_ONE_SHOT |

## MLX90632 — Measurement Type

The MLX90632 measurement select enum has non-contiguous values.

| JSON Index | Option Label | Enum Value | Enum Name |
|------------|-------------|------------|-----------|
| 0 | Medical | 0x00 | MLX90632_MEAS_MEDICAL |
| 1 | Extended Range | 0x11 | MLX90632_MEAS_EXTENDED_RANGE |
