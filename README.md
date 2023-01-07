# LARUS Protocol

This document describes the LARUS serial port protocol.

**Version: 0.1**

## Specification

The Larus Flight Information Sensor System for Gliders provides essential information for glider navigation like

- position,
- attitude and heading AHRS,
- GNSS/INS-based ultra-fast variometer,
- real-time wind measurement,
- air-density measurement

For the GNSS position, regular NMEA 0183 sentences are used. Information regarding pressure measurements and voltage measurements are send using the OpenVario protocol as published on https://github.com/Turbo87/openvario-protocol.

For the attitude and wind information, a separate protocol has been defined as it is not (yet) supported by the OpenVario protocol. This LARUS serial port protocol is built around the `$PLARx` NMEA sentence. The `x` is a `W` when wind information is being sent, `A` when attitude information is shown and `D` for the ratio of observed air density divided by the ICAO standard air density.

## Available sentences

### Wind

           1   2 3   4 5 6
           |   | |   | | |
    $PLARW,x.x,a,x.x,a,a*hh<CR><LF>
    
This sentence gives information about the both the average and instantaneous wind. The different fields have the following meaning:
 
  1) Wind Angle, 0 to 360 degrees
  2) Reference, R = Relative, T = True
  3) Wind Speed
  4) Wind Speed Units, K (kph) / M (m/s) / N (kts)
  5) (A)verage or (I)nstantaneous wind
  6) Status, A = Data Valid
  7) Checksum

### Attitude

           1   2   3   4
           |   |   |   |  
    $PLARA,x.x,x.x,x.x*hh<CR><LF>

This sentence gives information about the current attitude. The different fields have the following meaning:

  1) Roll angle (positive while turning right)
  2) Pitch angle (positive when nose up)
  3) Yaw angle (true heading)
  4) Checksum

### Observed density

           1   2
           |   |  
    $PLARD,x.x*hh<CR><LF>

This sentence gives information about the observed air density. The different fields have the following meaning:

  1) Observed air density divided by the ICAO standard air density 1.2250 kg/m3
  2) Checksum

## Checksum

The checksum is a regular NMEA checksum and is calculated over the NMEA string between `$` and `*`. The checksum is a hexadecimal number representing the XOR of all bytes.
