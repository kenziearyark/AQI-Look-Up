# PurpleAir AQI for Apple Shortcuts

An Apple Shortcut that retrieves real-time PM2.5 data from a PurpleAir sensor, applies an air-quality correction formula, and calculates the US EPA Air Quality Index (AQI).

No external AQI calculation service is required.

## Features

- Fetches sensor readings using the PurpleAir API
- Extracts PM2.5 and relative humidity
- Applies the US EPA PurpleAir correction
- Calculates AQI using EPA breakpoints
- Supports different air quality categories
- Runs directly in Apple Shortcuts

## Requirements

- iPhone or iPad with Apple Shortcuts
- Internet connection
- PurpleAir API key
- PurpleAir sensor ID

## How It Works

### 1. Retrieve Sensor Data

The Shortcut requests sensor data from the PurpleAir API.

Example JSON fields:

```json
{
  "pm2.5_atm": 70,
  "humidity": 48
}
```

### 2. Correct PM2.5

For outdoor PurpleAir sensors, the following correction applies when ATM PM2.5 is between 50 and 210 µg/m³:

```text
C = 0.786P - 0.0862H + 5.75
```

Where:

| Variable | Description |
|---|---|
| P | Raw ATM PM2.5 concentration (µg/m³) |
| H | Relative humidity (%) |
| C | Corrected PM2.5 concentration (µg/m³) |

Other concentration ranges require different correction equations.

### 3. Calculate AQI

The US EPA uses linear interpolation between concentration breakpoints.

```text
AQI = ((IH - IL) / (CH - CL)) * (C - CL) + IL
```

| Variable | Description |
|---|---|
| C | Corrected PM2.5, truncated to one decimal |
| CL | Lower concentration breakpoint |
| CH | Upper concentration breakpoint |
| IL | Lower AQI breakpoint |
| IH | Upper AQI breakpoint |

### EPA PM2.5 Breakpoints

| PM2.5 (µg/m³) | AQI | Category |
|---|---|---|
| 0.0–9.0 | 0–50 | Good |
| 9.1–35.4 | 51–100 | Moderate |
| 35.5–55.4 | 101–150 | Unhealthy for Sensitive Groups |
| 55.5–125.4 | 151–200 | Unhealthy |
| 125.5–225.4 | 201–300 | Very Unhealthy |
| 225.5–325.4 | 301–500 | Hazardous |

PM2.5 is truncated to one decimal place before selecting a breakpoint. The resulting AQI is rounded to the nearest integer.

## Example Calculation

Given:

```text
P = 70
H = 48
```

Corrected concentration:

```text
C = 0.786(70) - 0.0862(48) + 5.75
C = 56.6324
```

After truncation:

```text
C = 56.6
```

AQI:

```text
AQI = ((200 - 151) / (125.4 - 55.5)) * (56.6 - 55.5) + 151
AQI ≈ 152
```

**Result: AQI 152 (Unhealthy)**

## Limitations

- The Shortcut currently uses the latest sensor reading rather than the official 24-hour PM2.5 averaging method.
- The documented correction equation only covers ATM PM2.5 between 50 and 210 µg/m³.
- Results may differ from the PurpleAir map because of averaging, processing, or sampling differences.
- Sensor readings and calculated AQI values are estimates, not certified regulatory measurements.

## References

- [PurpleAir Data Calibrations](https://community.purpleair.com/t/purpleair-data-calibrations/103)
- [US EPA AQI Breakpoints](https://aqs.epa.gov/aqsweb/documents/codetables/aqi_breakpoints.html)
- [PurpleAir API](https://api.purpleair.com/)

## License

Add a license if you intend to distribute or allow others to reuse this project.
