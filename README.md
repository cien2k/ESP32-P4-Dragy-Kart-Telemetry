# ESP32-P4 Dragy Lite Kart Racing Telemetry

A custom kart racing telemetry and lap timing device based on the **ESP32-P4**, designed to work with **Dragy Lite 25 Hz GNSS data**.

The goal of this project is to create a compact standalone telemetry display for karting that can receive live GNSS data, calculate lap timing and live delta, display key driving information, and record raw telemetry data to an SD card for later analysis.

## Project Goals

The system is intended to provide:

* Live GPS/GNSS data acquisition
* 25 Hz position and speed updates
* Lap timing
* Automatic start/finish line detection
* Sector timing
* Live delta compared with the best or reference lap
* Predicted lap time
* Speed display
* Racing line analysis
* Corner entry, apex and exit analysis
* Raw GNSS data logging
* Parsed telemetry logging
* Session diagnostics
* Offline lap comparison

## Hardware

### Main Controller

**ESP32-P4**

Planned development board:

**Waveshare ESP32-P4-WIFI6-Touch-LCD-4.3**

Main features:

* ESP32-P4
* ESP32-C6 wireless coprocessor
* 4.3-inch IPS display
* 480 × 800 resolution
* Capacitive touch
* MIPI-DSI display interface
* 32 MB PSRAM
* 32 MB Flash
* microSD / TF card slot
* Wi-Fi
* Bluetooth Low Energy

## GNSS Source

### Dragy Lite

The primary GNSS source for the project is **Dragy Lite**.

Dragy Lite provides:

* 25 Hz GNSS positioning
* Standalone operation
* UART output
* Dragy Open API support
* Internal data storage

The project is intended to use the Dragy Lite Open API / UART interface to receive live GNSS data directly on the ESP32-P4.

The exact UART protocol implementation will be added after receiving the official Dragy Lite Open API documentation.

## Planned Data Flow

```text
Dragy Lite
   |
   | UART / Open API
   v
ESP32-P4
   |
   +--> Raw GNSS Buffer
   |
   +--> microSD Raw Logger
   |
   +--> GNSS Parser
   |
   +--> Lap Detection
   |
   +--> Live Delta Engine
   |
   +--> Racing Line Analysis
   |
   +--> Display
```

## Data Logging

Raw GNSS data will always have the highest priority.

The system is designed so that display rendering, telemetry calculations, or parsing cannot block the GNSS receiver.

Planned session structure:

```text
/sessions/
    session_0001/
        metadata.json
        gps_raw.bin
        gps_raw_index.csv
        gps_parsed.csv
        events.csv
        diagnostics.csv
```

### Raw Logging

The original data received from Dragy Lite will be stored without modification.

This allows the GNSS protocol to be reprocessed later without requiring another track session.

### Parsed Telemetry

Where supported by the Dragy Lite API, parsed data may include:

* GNSS timestamp
* Latitude
* Longitude
* Altitude
* Ground speed
* Heading
* Fix status
* Satellite count
* Horizontal accuracy
* Vertical accuracy
* Speed accuracy
* Heading accuracy

Additional fields may be added depending on the official Dragy Lite API specification.

## Live Delta

One of the main goals of the project is real-time lap delta calculation.

The system will compare the current position on the track against a reference lap.

Example:

```text
BEST LAP
1:18.421

CURRENT DELTA
-0.37 s

PREDICTED LAP
1:18.051
```

The delta value will update continuously using incoming GNSS data.

## Racing Line Analysis

Recorded GNSS data will also be used to compare driving lines.

Future analysis may include:

* Track position comparison
* Corner entry line
* Apex position
* Corner exit line
* Minimum corner speed
* Entry speed
* Exit speed
* Braking point
* Acceleration point
* Time gained or lost through individual corners

The objective is not only to find the shortest line, but to determine which trajectory produces the lowest total time through a section of the track.

## Optimal Lap

The system may later generate an optimal theoretical lap using the fastest sections from multiple recorded laps.

Example:

```text
Sector / Corner 1 -> Lap 4
Sector / Corner 2 -> Lap 7
Sector / Corner 3 -> Lap 5
Sector / Corner 4 -> Lap 9
```

These sections can be combined to estimate the driver's theoretical best lap.

## Software

Primary development environment:

* ESP-IDF
* C / C++
* FreeRTOS
* LVGL or ESP-IDF display components

Planned software architecture:

```text
gps_transport
raw_capture
gps_parser
storage
lap_engine
delta_engine
track_engine
ui
diagnostics
```

## Reliability

Because this device is intended for track use, data integrity is more important than UI performance.

Design priorities:

1. Do not lose raw GNSS data
2. Do not block GPS reception
3. Buffer SD writes
4. Detect queue overflow
5. Detect dropped packets
6. Record parser errors
7. Record SD write latency
8. Recover from temporary GNSS disconnection

Target endurance test:

**6 hours continuous operation with zero raw GNSS packet loss.**

## Development Status

Current status:

**Early prototype / hardware integration stage**

Planned development order:

1. ESP32-P4 board bring-up
2. LCD and touch validation
3. microSD logging
4. GNSS UART capture
5. Dragy Lite protocol integration
6. GNSS parsing
7. Lap detection
8. Sector timing
9. Live delta
10. Racing line analysis

## Dragy Lite API Integration

This project is currently waiting for access to the official **Dragy Lite Open API / UART documentation**.

The following information is required for implementation:

* UART pinout
* UART voltage level
* Baud rate
* Packet format
* Message framing
* Output rate configuration
* 25 Hz data output
* GNSS time format
* Latitude / longitude format
* Speed format
* Heading format
* Accuracy fields
* Commands required to enable UART output

Once the official documentation is available, the Dragy Lite communication layer will be implemented as an independent module.

## Project Purpose

This is an experimental hardware and software development project focused on kart racing telemetry, lap timing and driver analysis.

It is intended for research, development and personal motorsport use.

## License

License information will be added later as the project develops.
