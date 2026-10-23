# NMEA XDR for WeeWX

Add pressure or temperature from serial NMEA 0183 XDR sentences to an existing
WeeWX station's LOOP packets. This is an additional service; keep your weather
station driver. Upstream reports use with the Dracal USB-BAR20-N barometer.

Copyright 2019-2020 Tom Keffer. Distributed under [GPLv3](LICENSE.txt).

This repository mirrors upstream source. Install the separately repaired catalog
archive **0.10.post1** below for the maintained WeeWX 5 distribution.
A branch download does not contain those catalog repairs.

## Install

The catalog release requires Python >=3.10,<3.15 and WeeWX >=5.5,<6.

Install `pyserial` in the WeeWX Python environment:

```sh
python -m pip install pyserial
```

Use the `weectl` from your WeeWX Python environment:

```sh
weectl extension install https://github.com/weewx-packages/catalog/releases/download/nmea-xdr/v0.10.post1/nmea-xdr-0.10.post1.tar.gz --config=/path/to/weewx.conf
```

Replace `/path/to/weewx.conf` with your station configuration. Stock WeeWX 5
accepts this fixed URL or a downloaded copy of the same archive. No catalog
integration is required. Restart WeeWX yourself after configuration changes.

## Select the serial data

The installer registers `user.nmea-xdr.XDR` in
`[Engine][[Services]] data_services`. Preserve the other services in that list.
The module filename contains a hyphen; do not replace the service name with a
filename or `nmea-xdr.py`.

Edit the installed stanza to match the device:

```ini
[XDR]
    port = /dev/ttyACM0
    baudrate = 9600
    timeout = 5
    max_packets = 5
    [[sensor_map]]
        pressure = P
```

`P` selects the XDR transducer type, not its sensor name. Add `outTemp = C` only
if you want temperature readings to replace your station's `outTemp`. The
service supports Celsius, Fahrenheit and bar input units, converts them to the
LOOP packet's unit system and discards sentences with invalid checksums.

Give the WeeWX service account access to the port. On each LOOP event the service
processes queued sentences, keeping at most `max_packets` pending sentences.
It overwrites mapped observations in that packet. It neither polls a cloud API
nor creates LOOP packets when the main station is silent. Additional observation
names need matching archive columns. No standalone CLI or hardware check is
provided by this documentation refresh.

The installed extension is named `xdr`. Upstream source and history:
[tkeffer/weewx-nmea-xdr](https://github.com/tkeffer/weewx-nmea-xdr).

<!-- Documentation updated 2026-10-23 by weewx-packages. -->
