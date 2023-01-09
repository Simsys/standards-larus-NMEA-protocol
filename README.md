# LARUS Protocol **Version 0.1**

This document describes the LARUS serial port protocol.

The Larus sensor system can provide more information than
the sensors that have been used in former navigation
computers for gliders. Therefore the protocols need to be
extended to be able to transfer new messages.

Because of the best fit we have decided to extend the
existing XCsoar OpenVario driver.

## Specification

The Larus Flight Information Sensor System for Gliders provides essential information for glider navigation like

- Position and Time (Just the standard GPS stuff)
- Attitude and Heading (AHRS)
- GNSS/INS-based ultra-fast variometer and DSP-filtered average variometer
- Real-time wind measurement
- Air-density measurement

For the GNSS position, regular NMEA 0183 sentences are used. Information regarding pressure measurements and voltage measurements are send using the OpenVario protocol as published on https://github.com/Turbo87/openvario-protocol.

For the attitude, wind, and air density information, a separate protocol has been defined as it is not (yet) supported by the OpenVario protocol. This LARUS serial port protocol is built around the `$PLARx` NMEA sentence. The `x` is a `W` when wind information is being sent, `A` when attitude information is shown and `D` for the instant air density.

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

           1   2 3
           |   | |  
    $PLARD,xxxx.x,a*hh<CR><LF>

This sentence gives information about the instant air density at the current altitude. The different fields have the following meaning:

  1) Instant air density in g/m^3.
  2) a = (M)easured or (E)stimated
  3) Checksum

## Checksum

The checksum is the standard NMEA checksum. It is calculated over the NMEA string between `$` and `*`. The checksum is a hexadecimal number representing the XOR of all bytes.
