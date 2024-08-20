pygnssutils
=======

[Current Status](#currentstatus) |
[Installation](#installation) |
[gnssdump CLI](#gnssdump) |
[gnssserver CLI](#gnssserver) |
[gnssntripclient CLI](#gnssntripclient) |
[gnssmqttclient CLI](#gnssmqttclient) |
[ubxsimulator](#ubxsimulator) |
[ubxsetrate CLI](#ubxsetrate) |
[ubxcompare CLI](#ubxcompare) |
[Troubleshooting](#troubleshooting) |
[Graphical Client](#gui) |
[Author & License](#author)

pygnssutils is an original series of GNSS CLI utilities and Python classes built around the following core libraries from the same stable:

1. [pyubx2](https://github.com/semuconsulting/pyubx2) - UBX parsing and generation library, which in turn utilises:
1. [pynmeagps](https://github.com/semuconsulting/pynmeagps) - NMEA parsing and generation library
1. [pyrtcm](https://github.com/semuconsulting/pyrtcm) - RTCM3 parsing library
1. [pyspartn](https://github.com/semuconsulting/pyspartn) - SPARTN parsing library

Originally developed in support of the [PyGPSClient](https://github.com/semuconsulting/PyGPSClient) GUI GNSS application, the utilities provided by pygnssutils can also be used in their own right:

1. `GNSSStreamer` class and its associated [`gnssdump`](#gnssdump) CLI utility. This is essentially a configurable input/output wrapper around the [`pyubx2.UBXReader`](https://github.com/semuconsulting/pyubx2#reading) class with flexible message formatting and filtering options for NMEA, UBX and RTCM3 protocols.
1. `GNSSSocketServer` class and its associated [`gnssserver`](#gnssserver) CLI utility. This implements a TCP Socket Server for GNSS data streams which is also capable of being run as a simple NTRIP Server.
1. `GNSSNTRIPClient` class and its associated [`gnssntripclient`](#gnssntripclient) CLI utility. This implements
a simple NTRIP Client which receives RTCM3 or SPARTN correction data from an NTRIP Server and (optionally) sends this to a
designated output stream.
1. `GNSSMQTTClient` class and its associated [`gnssmqttclient`](#gnssmqttclient) CLI utility. This implements
a simple SPARTN IP (MQTT) Client which receives SPARTN correction data from an SPARTN IP location service sends this to a
designated output stream.
1. [`ubxsimulator`](#ubxsimulator) utility. This provides a simple simulation of a GNSS receiver serial stream by generating synthetic UBX or NMEA messages based on parameters defined in a json configuration file.
1. [`ubxsave`](#ubxsave) CLI utility. This saves a complete set of configuration data from any Generation 9+ u-blox device (e.g. NEO-M9N or ZED-F9P) to a file. The file can then be reloaded to any compatible device using the `ubxload` utility.
1. [`ubxload`](#ubxload) CLI utility. This reads a file containing binary configuration data and loads it into any compatible Generation 9+ u-blox device (e.g. NEO-M9N or ZED-F9P).
1. [`ubxsetrate`](#ubxsetrate) CLI utility. A simple utility which sets NMEA or UBX message rates on u-blox GNSS receivers.
1. [`ubxcompare`](#ubxcompare) CLI utility. Utility for comparing two or more u-blox config files in either text (\*.txt) or binary (\*.ubx) format. Output files from the `ubxsave` utility can be used as binary input files.

The pygnssutils homepage is located at [https://github.com/semuconsulting/pygnssutils](https://github.com/semuconsulting/pygnssutils).

## <a name="currentstatus">Current Status</a>

![Status](https://img.shields.io/pypi/status/pygnssutils)
![Release](https://img.shields.io/github/v/release/semuconsulting/pygnssutils?include_prereleases)
![Build](https://img.shields.io/github/actions/workflow/status/semuconsulting/pygnssutils/main.yml?branch=main)
![Release Date](https://img.shields.io/github/release-date-pre/semuconsulting/pygnssutils)
![Last Commit](https://img.shields.io/github/last-commit/semuconsulting/pygnssutils)
![Contributors](https://img.shields.io/github/contributors/semuconsulting/pygnssutils.svg)
![Open Issues](https://img.shields.io/github/issues-raw/semuconsulting/pygnssutils)

Sphinx API Documentation in HTML format is available at [https://www.semuconsulting.com/pygnssutils](https://www.semuconsulting.com/pygnssutils).

Contributions welcome - please refer to [CONTRIBUTING.MD](https://github.com/semuconsulting/pygnssutils/blob/master/CONTRIBUTING.md).

[Bug reports](https://github.com/semuconsulting/pygnssutils/blob/master/.github/ISSUE_TEMPLATE/bug_report.md) and [Feature requests](https://github.com/semuconsulting/pygnssutils/blob/master/.github/ISSUE_TEMPLATE/feature_request.md) - please use the templates provided. For general queries and advice, post a message to one of the [pygnssutils Discussions](https://github.com/semuconsulting/pygnssutils/discussions) channels.

---
## <a name="installation">Installation</a>

![Python version](https://img.shields.io/pypi/pyversions/pygnssutils.svg?style=flat)
[![PyPI version](https://img.shields.io/pypi/v/pygnssutils.svg?style=flat)](https://pypi.org/project/pygnssutils/)
![PyPI downloads](https://img.shields.io/pypi/dm/pygnssutils.svg?style=flat)

`pygnssutils` is compatible with Python 3.8 - 3.13*¹*.

In the following, `python3` & `pip` refer to the Python 3 executables. You may need to substitute `python` for `python3`, depending on your particular environment (*on Windows it's generally `python`*). **It is strongly recommended that** the Python 3 binaries (\Scripts or /bin) and site_packages directories are included in your PATH (*most standard Python 3 installation packages will do this automatically if you select the 'Add to PATH' option during installation*).

The recommended way to install the latest version of `pygnssutils` is with [pip](http://pypi.python.org/pypi/pip/):

```shell
python3 -m pip install --upgrade pygnssutils
```

If required, `pygnssutils` can also be installed into a virtual environment, e.g.:

```shell
python3 -m pip install --user --upgrade virtualenv
python3 -m virtualenv env
source env/bin/activate (or env\Scripts\activate on Windows)
python3 -m pip install --upgrade pygnssutils
...
deactivate
```

*¹* At time of writing, Python >= 3.13.0b4 (*pre-release*) requires a pre-release version of the `cffi` library (which is a dependency of `cryptography`):
```shell
python3.13 -m pip install --pre cffi==1.17.0rc1
```

For [Conda](https://docs.conda.io/en/latest/) users, `pygnssutils` is also available from [conda forge](https://github.com/conda-forge/pygnssutils-feedstock):

[![Anaconda-Server Badge](https://anaconda.org/conda-forge/pygnssutils/badges/version.svg)](https://anaconda.org/conda-forge/pygnssutils)
[![Anaconda-Server Badge](https://img.shields.io/conda/dn/conda-forge/pygnssutils)](https://anaconda.org/conda-forge/pygnssutils)

```shell
conda install -c conda-forge pygnssutils
```

---
## <a name="gnssdump">GNSSStreamer and gnssdump CLI</a>

```
class pygnssutils.gnssdump.GNSSStreamer(**kwargs)
```

`gnssdump` is a command line utility which parses and formats the NMEA, UBX or RTCM3 output of a GNSS receiver. The utility can capture data from a variety of input sources (including `--port` serial, `--socket` socket and `--filename` file) and output it to stdout (terminal) or to a designated output handler (`--cliout`: (0) stdout (terminal), (1) file, (2) serial, (3) TCP socket server or (4) Python lambda function). It can output in a variety of formats (`--format`: (1) parsed, (2) raw binary, (4) hexadecimal string, (8) tabulated hexadecimal, (16) parsed as string, (32) JSON, or any OR'd combination thereof). It offers a variety of data filtering options (`--protfilter`, `--msgfilter`) based on message protocol, identity and frequency. 

You could, for example, output the parsed version of a UBX message alongside its tabular hexadecimal representation.

Any one of the following data stream specifiers must be provided:
- `port`: serial port e.g. `COM3` or `/dev/ttyACM1`
- `filename`: fully qualified path to binary input file e.g. `/logs/logfile.bin`
- `socket`: socket e.g. `192.168.0.72:50007` (port must be specified)
- `stream`: any other instance of a stream class which implements a `read(n) -> bytes` method

For help and full list of optional arguments, type:

```shell
gnssdump -h
```

Command line arguments can be stored in a configuration file and invoked using the `-C` or `--config` argument. The location of the configuration file can be set in environment variable `GNSSDUMP_CONF`.

`GNSSStreamer` - the underlying Python class of `gnssdump` - is essentially a configurable input/output wrapper around the [`pyubx2.UBXReader`](https://github.com/semuconsulting/pyubx2#reading) class which can be used within Python scripts.

Refer to the [Sphinx API documentation](https://www.semuconsulting.com/pygnssutils/pygnssutils.html#module-pygnssutils.gnssdump) for further details.

### CLI Usage:

Assuming the Python 3 scripts (bin) directory is in your PATH, the CLI utility may be invoked from the shell thus:

Serial input example (with evaluable Python lambda expression as simple output handler):

```shell
gnssdump --port /dev/ttyACM1 --baudrate 9600 --timeout 5 --quitonerror 1 --protfilter 2 --msgfilter NAV-PVT --cliout 4 --output "lambda msg: print(f'lat: {msg.lat}, lon: {msg.lon}')" --verbosity 2
```
```
2024-08-15 09:31:48.68 - INFO - pygnssutils.gnssstreamer - Parsing GNSS data stream from: Serial<id=0x101a339a0, open=True>(port='/dev/tty.usbmodem101', baudrate=9600, bytesize=8, parity='N', stopbits=1, timeout=5, xonxoff=False, rtscts=False, dsrdtr=False)...
lat: 37.23345, lon: -115.81512
lat: 37.23347, lon: -115.81515
lat: 37.23343, lon: -115.81513
```

File input example (in parsed and tabulated hexadecimal formats):

```shell
gnssdump --filename pygpsdata.log --quitonerror 2 --format 9 --verbosity 2
```
```
2024-08-15 09:31:48.68 - INFO - pygnssutils.gnssstreamer - Parsing GNSS data stream from file: <_io.BufferedReader name='pygpsdata.log'>...

<UBX(NAV-STATUS, iTOW=09:47:37, gpsFix=3, gpsFixOk=1, diffSoln=0, wknSet=1, towSet=1, diffCorr=0, carrSolnValid=1, mapMatching=0, psmState=0, spoofDetState=1, carrSoln=0, ttff=33377, msss=1912382)>
000: b562 0103 1000 f80c da1b 03dd 0208 6182  | b'\xb5b\x01\x03\x10\x00\xf8\x0c\xda\x1b\x03\xdd\x02\x08a\x82' |
016: 0000 3e2e 1d00 633d                      | b'\x00\x00>.\x1d\x00c=' |

<UBX(NAV-DOP, iTOW=09:47:37, gDOP=1.55, pDOP=1.32, tDOP=0.8, vDOP=1.11, hDOP=0.72, nDOP=0.59, eDOP=0.42)>
000: b562 0104 1200 f80c da1b 9b00 8400 5000  | b'\xb5b\x01\x04\x12\x00\xf8\x0c\xda\x1b\x9b\x00\x84\x00P\x00' |
016: 6f00 4800 3b00 2a00 9b75                 | b'o\x00H\x00;\x00*\x00\x9bu' |

<UBX(NAV-TIMEGPS, iTOW=09:47:37, fTOW=422082, week=2216, leapS=18, towValid=1, weekValid=1, leapSValid=1, tAcc=10)>
000: b562 0120 1000 f80c da1b c270 0600 a808  | b'\xb5b\x01 \x10\x00\xf8\x0c\xda\x1b\xc2p\x06\x00\xa8\x08' |
016: 1207 0a00 0000 3566                      | b'\x12\x07\n\x00\x00\x005f' |
```

Socket input example (in JSON format):

```shell
gnssdump --socket 192.168.0.20:50010 --format 32 --msgfilter 1087 --verbosity 2
```
```
2024-08-15 09:31:48.68 - INFO - pygnssutils.gnssstreamer - Parsing GNSS data stream from: <socket.socket fd=3, family=AddressFamily.AF_INET, type=SocketKind.SOCK_STREAM, proto=0, laddr=('127.0.0.1', 57399), raddr=('127.0.0.1', 50010)>...

{"GNSS_Messages: [{"class": "<class 'pyrtcm.rtcmmessage.RTCMMessage'>", "identity": "1087", "payload": {"DF002": 1087, "DF003": 0, "GNSSEpoch": 738154640, "DF393": 1, "DF409": 0, "DF001_7": 0, "DF411": 0, "DF412": 0, "DF417": 0, "DF418": 0, "DF394": 1152921504606846976, "NSat": 1, "DF395": 1073741824, "NSig": 1, "DF396": 1, "DF405_01": 0.00050994, "DF406_01": 0.00194752, "DF407_01": 102, "DF420_01": 0, "DF408_01": 0, "DF404_01": 0.5118}},...]}
```

Output file example (this filters unwanted UBX config & debug messages from a u-center .ubx file):

```shell
gnssdump --filename COM6__9600_220623_093412.ubx --protfilter 1 --format 2 --verbosity 0 --cliout 1 --output COM6__9600_220623_093412_filtered.ubx
```

Output to socket server example, using remote instances of gnssdump as socket clients:

**gnssdump as socket server:**

```shell
gnssdump --port /dev/tty.usbmodem101 --cliout 3 --output localhost:50011 --format 2 --verbosity 2
```
```
2024-08-15 09:00:04.769 - INFO - pygnssutils.gnssstreamer - Parsing GNSS data stream from: Serial<id=0x1016467a0, open=True>(port='/dev/tty.usbmodem101', baudrate=38400, bytesize=8, parity='N', stopbits=1, timeout=3, xonxoff=False, rtscts=False, dsrdtr=False)...
2024-08-15 09:00:09.952 - INFO - pygnssutils.socket_server - client ('192.168.1.58', 57964) has connected
2024-08-15 09:00:23.839 - INFO - pygnssutils.socket_server - client ('192.168.1.36', 57968) has connected
2024-08-15 09:00:34.29 - INFO - pygnssutils.socket_server - client ('192.168.1.36', 57968) has disconnected
2024-08-15 09:00:36.37 - INFO - pygnssutils.socket_server - client ('192.168.1.58', 57964) has disconnected
^C2024-08-15 09:00:35.196 - INFO - pygnssutils.gnssstreamer - Messages input:    {'NAV-DOP': 8, 'NAV-PVT': 31, 'NAV-SAT': 8}
2024-08-15 09:00:35.197 - INFO - pygnssutils.gnssstreamer - Messages filtered: {}
2024-08-15 09:00:35.197 - INFO - pygnssutils.gnssstreamer - Messages output:   {'NAV-DOP': 8, 'NAV-PVT': 31, 'NAV-SAT': 8}
2024-08-15 09:00:35.197 - INFO - pygnssutils.gnssstreamer - Streaming terminated, 47 messages processed with 0 errors.
```

**gnssdump as socket client:**

```shell
gnssdump -S localhost:50011
```
```
<UBX(NAV-PVT, iTOW=07:56:45, year=2024, month=8, day=15, hour=7, min=56, second=45, validDate=1, validTime=1, fullyResolved=1, validMag=0, tAcc=27, nano=376074, fixType=3, gnssFixOk=1, diffSoln=0, psmState=0, headVehValid=0, carrSoln=0, confirmedAvai=1, confirmedDate=1, confirmedTime=1, numSV=30, lon=-115.81512, lat=37.23345, height=5278, hMSL=5264, hAcc=2840, vAcc=2527, velN=-5, velE=-7, velD=8, gSpeed=8, headMot=0.0, sAcc=223, headAcc=180.0, pDOP=0.91, invalidLlh=0, lastCorrectionAge=0, reserved0=1044570318, headVeh=0.0, magDec=0.0, magAcc=0.0)>
...
```


## <a name="gnssserver">GNSSSocketServer and gnssserver CLI</a>

```
class pygnssutils.gnssserver.GNSSSocketServer(**kwargs)
```

`GNSSSocketServer` is essentially a wrapper around the `GNSSStreamer` and `SocketServer` classes (the latter based on the native Python `ThreadingTCPServer` framework) which uses queues to transport data between the two classes.

### CLI Usage - Default Mode:

In its default configuration (`ntripmode=0`) `gnssserver` acts as an open, unauthenticated CLI TCP socket server, reading the binary data stream from a host-connected GNSS receiver and broadcasting the data to any local or remote TCP socket client capable of parsing binary GNSS data.

It supports most of `gnssdump`'s formatting capabilities and could be configured to output a variety of non-binary formats (including, for example, JSON or hexadecimal), but the client software would need to be capable of parsing data in such formats.

Assuming the Python 3 scripts (bin) directory is in your PATH, the CLI utility may be invoked from the shell thus:

```shell
gnssserver --inport "/dev/tty.usbmodem101" --baudrate 115200 --hostip localhost --outport 50012 --verbosity 2
```
```
2024-08-15 09:12:25.443 - INFO - pygnssutils.gnssserver - Starting server (type CTRL-C to stop)...
2024-08-15 09:12:25.443 - INFO - pygnssutils.gnssserver - Starting input thread, reading from /dev/tty.usbmodem101...
2024-08-15 09:12:25.461 - INFO - pygnssutils.gnssstreamer - Parsing GNSS data stream from: Serial<id=0x103966e60, open=True>(port='/dev/tty.usbmodem101', baudrate=115200, bytesize=8, parity='N', stopbits=1, timeout=3, xonxoff=False, rtscts=False, dsrdtr=False)...
2024-08-15 09:12:25.949 - INFO - pygnssutils.gnssserver - Starting output thread, broadcasting on localhost:50012...
2024-08-15 09:12:36.953 - INFO - pygnssutils.socket_server - client ('192.168.1.34', 58207) has connected
2024-08-15 09:12:36.953 - INFO - pygnssutils.gnssserver - Client ('192.168.1.34', 58207) has connected. Total clients: 1
2024-08-15 09:12:43.35 - INFO - pygnssutils.socket_server - client ('192.168.1.34', 58207) has disconnected
2024-08-15 09:12:43.35 - INFO - pygnssutils.gnssserver - Client ('192.168.1.34', 58207) has disconnected. Total clients: 0
```

`gnssserver` can be run as a daemon process (or even a service) but note that abrupt termination (i.e. without invoking the internal `server.shutdown()` method) may result in the designated TCP socket port being unavailable for a short period - this is operating system dependant.

Command line arguments can be stored in a configuration file and invoked using the `-C` or `--config` argument. The location of the configuration file can be set in environment variable `GNSSSERVER_CONF`.

For help and full list of optional arguments, type:

```shell
gnssserver -h
```

Refer to the [Sphinx API documentation](https://www.semuconsulting.com/pygnssutils/pygnssutils.html#module-pygnssutils.gnssserver) for further details.

### CLI Usage - NTRIP Mode:

`gnssserver` can also be configured to act as a single-mountpoint NTRIP Server/Caster (`ntripmode=1`), broadcasting RTCM3 RTK correction data to any authenticated NTRIP client on the standard 2101 port using the mountpoint name `pygnssutils` (**NB**: to use with standard NTRIP clients, output format must be set to binary (2) - this is the default, so the argument can be omitted): 

```shell
gnssserver --inport "/dev/tty.usbmodem14101" --hostip 192.168.0.20 --outport 2101 --ntripmode 1 --protfilter 4 --format 2 --ntripuser myuser --ntrippassword mypassword --verbosity 2
```

**NOTE THAT** this configuration is predicated on the host-connected receiver being an RTK-capable device (e.g. the u-blox ZED-F9P) operating in 'Base Station' mode (either 'SURVEY_IN' or 'FIXED') and outputting the requisite RTCM3 RTK correction messages (1005, 1077, 1087, 1097, 1127, 1230). NTRIP server login credentials are set via command line arguments or environment variables `PYGPSCLIENT_USER` and `PYGPSCLIENT_PASSWORD`. 

### Clients

`gnssserver` will work with any client capable of parsing binary GNSS data from a TCP socket. Suitable clients include, *but are not limited to*:

1) (in default mode) pygnssutils's `gnssdump` cli utility invoked thus:

```shell
gnssdump --socket hostip:outport
```

2) (in NTRIP mode) Any standard NTRIP client, including BKG's [NTRIP client (BNC)](https://igs.bkg.bund.de/ntrip/download), ublox's [legacy ucenter NTRIP client](https://www.u-blox.com/en/product/u-center), or pygnssutil's `gnssntripclient` cli utility invoked thus:

```shell
gnssntripclient -S hostip -P 2101 -M pygnssutils --ntripuser myuser --ntrippassword mypassword --verbosity 2
```

3) The [PyGPSClient GUI](https://github.com/semuconsulting/PyGPSClient?tab=readme-ov-file#ntripconfig) application.

---
## <a name="gnssntripclient">GNSSNTRIPClient and gnssntripclient CLI</a>
 
```
class pygnssutils.gnssntripclient.GNSSNTRIPClient(app=None, **kwargs)
```

The `GNSSNTRIPClient` class provides a basic NTRIP Client capability and forms the basis of a [`gnssntripclient`](#gnssntripclient) CLI utility. It receives RTCM3 or SPARTN correction data from an NTRIP server and (optionally) sends this to a designated output stream. NTRIP server login credentials are set via command line arguments or environment variables `PYGPSCLIENT_USER` and `PYGPSCLIENT_PASSWORD`.

### CLI Usage:

Assuming the Python 3 scripts (bin) directory is in your PATH, the CLI utility may be invoked from the shell thus:

To retrieve the sourcetable and determine the closest available mountpoint to the reference lat/lon, leave the mountpoint argument blank (the port defaults to 2101):

```shell
gnssntripclient --server rtk2go.com --port 2101 --https 0 --datatype RTCM --ntripversion 2.0 --ggainterval -1 --reflat 37.23 --reflon 115.81 --ntripuser myuser --ntrippassword mypassword --verbosity 2
```
```
2024-08-15 09:22:21.174 - INFO - pygnssutils.gnssntripclient - Closest mountpoint to reference location(37.23, 115.81) = MYBASE, 313.65 km.
2024-08-15 09:22:21.176 - INFO - pygnssutils.gnssntripclient - Complete sourcetable follows...
[['ACAKO', 'Kovin', 'RTCM 3.2', '1005(30),1074(1),1084(1),1094(1)', '2', 'GPS+GLO+GAL', 'SNIP', 'SRB', '44.75', '21.01', '1', '0', 'sNTRIP', 'none', 'B', 'N', '3200', ''], ['ACASU', 'Subotica', 'RTCM 3.2', '1005(30),1074(1),1084(1),1094(1)', '2', 'GPS+GLO+GAL', 'SNIP', 'SRB', '46.06', '19.52', '1', '0', 'sNTRIP', 'none', 'B', 'N', '3360', ''], ['ADS-SAH', 'Ciudad Real', 'RTCM 3.2', '1005(1),1074(1),1084(1),1094(1),1230(1)', '', 'GPS+GLO+GAL', 'SNIP', 'ESP', '39.05', '-4.06', '1', '0', 'sNTRIP', 'none', 'B', 'N', '0', ''], [['AGSSIAAP', 'Acheres', 'RTCM 3.0', '1004(1),1006(13),1012(1),1033(31)', '2', 'GPS+GLO', 'SNIP', 'FRA', '48.97', '2.17', '1', '0', 'sNTRIP', 'none', 'N', 'N', '2540', '']
...
```

To retrieve correction data from a designated mountpoint (this will send NMEA GGA position sentences to the server at intervals of 60 seconds, based on the supplied reference lat/lon):

```shell
gnssntripclient --server rtk2go.com --port 2101 --https 0 --mountpoint MYBASE --datatype RTCM --ggainterval 60 --reflat 37.23 --reflon 115.81 --ntripuser myuser --ntrippassword mypassword --verbosity 2
```
```
2024-08-15 09:24:34.872 - INFO - pygnssutils.gnssntripclient - Streaming RTCM data from rtk2go.com:2101/MYBASE ...
2024-08-15 09:24:35.897 - INFO - pygnssutils.gnssntripclient - RTCMMessage received: 1019
2024-08-15 09:24:35.898 - INFO - pygnssutils.gnssntripclient - RTCMMessage received: 1020
2024-08-15 09:24:35.898 - INFO - pygnssutils.gnssntripclient - RTCMMessage received: 1042
...
```

Command line arguments can be stored in a configuration file and invoked using the `-C` or `--config` argument. The location of the configuration file can be set in environment variable `GNSSNTRIPCLIENT_CONF`.

For help and full list of optional arguments, type:

```shell
gnssntripclient -h
```

Refer to the [Sphinx API documentation](https://www.semuconsulting.com/pygnssutils/pygnssutils.html#module-pygnssutils.gnssntripclient) for further details.

---
## <a name="gnssmqttclient">GNSSMQTTClient and gnssmqttclient CLI</a>
```
class pygnssutils.gnssmqttclient.GNSSMQTTClient(app=None, **kwargs)
```

The `GNSSMQTTClient` class provides a basic SPARTN IP (MQTT) Client capability and forms the basis of a [`gnssmqttclient`](#gnssmqttclient) CLI utility. It receives RTK correction data from a SPARTN IP (MQTT) location service (e.g. the u-blox / Thingstream PointPerfect service) and (optionally) sends this to a designated output stream.

### CLI Usage:

The `clientid` provided by the location service may be set as environment variable `MQTTCLIENTID`. If this environment variable is set and the TLS certificate (\*.crt) and key (\*.pem) files provided by the location service are placed in the user's `HOME` directory, the utility can use these as default settings and may be invoked without any arguments.

Assuming the Python 3 scripts (bin) directory is in your PATH, the CLI utility may be invoked from the shell thus (press CTRL-C to terminate):

```shell
gnssmqttclient --clientid yourclientid --server pp.services.u-blox.com --port 8883 --region eu --mode 0 --topic_ip 1 --topic_mga 1 --topic_key 1 --tlscrt '/Users/{your-user}/device-{your-clientid}-pp-cert.crt' --tlskey '/Users/{your-user}/device-{your-client-id}-pp-key.pem'} --spartndecode 0 --cliout 0 --verbosity 2
```
```
2024-08-15 09:14:50.544 - INFO - pygnssutils.gnssmqttclient - Starting MQTT client with arguments {'server': 'pp.services.u-blox.com', 'port': 8883, 'clientid': 'your-client-id', 'region': 'eu', 'mode': 0, 'topic_ip': 1, 'topic_mga': 1, 'topic_key': 1, 'tlscrt': '/Users/myuser/device-your-client-id-pp-cert.crt', 'tlskey': '/Users/myuser/device-your-client-id-pp-key.pem', 'spartndecode': 0, 'output': None}.
2024-08-15 09:14:50.840 - INFO - pygnssutils.gnssmqttclient - RXM-SPARTN-KEY
2024-08-15 09:14:50.854 - INFO - pygnssutils.gnssmqttclient - MGA-INI-TIME-UTC
2024-08-15 09:14:50.858 - INFO - pygnssutils.gnssmqttclient - MGA-GPS-EPH
...
```

Command line arguments can be stored in a configuration file and invoked using the `-C` or `--config` argument. The location of the configuration file can be set in environment variable `GNSSMQTTCLIENT_CONF`.

For help and full list of optional arguments, type:

```shell
gnssmqttclient -h
```

Refer to the [pyspartn documentation](https://github.com/semuconsulting/pyspartn?tab=readme-ov-file#reading) for further details on decrypting encrypted (`eaf=1`) SPARTN payloads.

---
## <a name="ubxsimulator">ubxsimulator utility</a>

### EXPERIMENTAL

Provides a simple simulation of a GNSS serial stream by generating synthetic UBX or NMEA messages based on parameters defined in a json configuration file. Can simulate a motion vector based on a specified course over ground and speed. Location of configuration file can be set via environment variable `UBXSIMULATOR`.

Example usage:

```shell
ubxsimulator --simconfigfile "/home/myuser/ubxsimulator.json" --interval 1000 --timeout 3 --verbosity 3
```

```python
from os import getenv
from pygnssutils import UBXSimulator, UBXSIMULATOR
from pyubx2 import UBXReader

with UBXSimulator(
    configfile=getenv(UBXSIMULATOR, "/home/myuser/ubxsimulator.json"),
    interval=1000,
    timeout=3,
) as stream:
    ubr = UBXReader(stream)
    for raw, parsed in ubr:
        print(parsed)
```

Generates mock acknowledgements (ACK-ACK) for valid incoming UBX commands and polls.

See sample [ubxsimulator.json](https://github.com/semuconsulting/pygnssutils/blob/main/examples/ubxsimulator.json) configuration file in the \examples folder, and the [Sphinx API documentation](https://www.semuconsulting.com/pygnssutils/pygnssutils.html#module-pygnssutils.ubxsimulator).

**NB:** Principally intended for testing Python GNSS application functionality. There is currently no attempt to simulate real-world satellite geodetics, though this could be done using e.g. the Python [`skyfield`](https://pypi.org/project/skyfield/) library and the  relevant satellite [TLE (orbital elements) data](https://celestrak.org/NORAD/elements/table.php?GROUP=gnss&FORMAT=tle). We may look into adding such functionality as and when time permits. Contributions welcome.

Command line arguments can be stored in a configuration file and invoked using the `-C` or `--config` argument. The location of the configuration file can be set in environment variable `UBXSIMULATOR_CONF`.

---
## <a name="ubxsave">ubxsave CLI</a>

*GENERATION 9+ DEVICES ONLY (e.g. NEO-M9N or ZED-F9P)*

```
class pygnssutils.ubxconfig.UBXSaver(file, stream, **kwargs)
```

CLI utility which saves Generation 9+ UBX device configuration data to a file. `ubxsave` polls configuration data via the device's serial port using a series of CFG-VALGET poll messages. It parses the responses to these polls, converts them to CFG-VALSET command messages and saves these to a binary file. This binary file can then be loaded into any compatible UBX device (e.g. via the `ubxload` utility) to restore the saved configuration.

The CFG-VALSET commands are stored as a single transaction. If one or more fails on reload, the entire set will be rejected.

*NB*: The utility relies on receiving a complete set of poll responses within a specified `waittime`. If the device is exceptionally busy or the transmit buffer is full, poll responses may be delayed or dropped altogether. If the utility reports errors, try increasing the waittime. 

### CLI Usage:

```shell
ubxsave --port /dev/ttyACM1 --baudrate 9600 --timeout 0.02 --outfile ubxconfig.ubx --verbosity 1
```

For help and full list of optional arguments, type:

```shell
ubxsave -h
```

---
## <a name="ubxload">ubxload CLI</a>

*GENERATION 9+ DEVICES ONLY (e.g. NEO-M9N or ZED-F9P)*

```
class pygnssutils.ubxconfig.UBXLoader(file, stream, **kwargs)
```

CLI utility which loads UBX configuration (CFG-VALSET) data from a binary file (e.g. one created by the `ubxsave` utility) and loads it into the volatile memory (RAM) of a compatible Generation 9+ UBX device via its serial port. It then awaits acknowledgements to this data and reports any errors.

### CLI Usage:

```shell
ubxload --port /dev/ttyACM1 --baudrate 9600 --timeout 0.05 --infile ubxconfig.ubx --verbosity 1
```

For help and full list of optional arguments, type:

```shell
ubxload -h
```

---
## <a name="ubxsetrate">ubxsetrate CLI</a>

```
class pygnssutils.ubxconfig.UBXSetRate(**kwargs)
```

A simple CLI utility to set NMEA or UBX message rates on u-blox receivers via a serial port.

### CLI Usage:

Assuming the Python 3 scripts (bin) directory is in your PATH, the CLI utility may be invoked from the shell thus:

This example sets the UBX NAV-HPPOSLLH message rate to 1:

```shell
ubxsetrate --port /dev/ttyACM0 --baudrate 38400 --msgClass 0x01 --msgID 0x14 --rate 1
```
```
Opening serial port /dev/ttyACM0 @ 38400 baud...

Sending configuration message <UBX(CFG-MSG, msgClass=NAV, msgID=NAV-HPPOSLLH, rateDDC=1, rateUART1=1, rateUART2=1, rateUSB=1, rateSPI=1, reserved=0)>...

Configuration message sent.
```

Refer to [pyubx2 documentation](https://github.com/semuconsulting/pyubx2/blob/master/pyubx2/ubxtypes_core.py) for available `msgClass` and `msgID` values. `msgClass` and `msgID` can be specified in either integer or hexadecimal formats.

Alternatively, the `msgClass` keyword can be set to one of the following group values (in which case the `msgID` keyword can be omitted):
- "allubx" - set rate for all available UBX NAV messages
- "minubx" - set rate for a minimum set of UBX NAV messages (NAV-PVT, NAV-SAT)
- "allnmea" - set rate for all available NMEA messages
- "minnmea" - set rate for a minimum set of NMEA messages (GGA, GSA, GSV, RMC, VTG)

For help and full list of optional arguments, type:

```shell
ubxsetrate -h
```

---
## <a name="ubxcompare">ubxcompare CLI</a>

```
class pygnssutils.ubxcompare.UBXCompare(infiles, form, diffsonly)
```

A simple CLI utility for comparing the contents of two or more u-blox configuration files. Files can be in text (\*.txt) format (as used by u-center or ArduSimple) or binary (\*.ubx) format (as used by [PyGPSClient](https://github.com/semuconsulting/PyGPSClient) or [ubxsave](#ubxsave)).

e.g. 

```shell
ubxcompare --infiles "simpleRTK2B_FW132_Rover_1Hz-00.txt, simpleRTK2B_FW132_Rover_10Hz-00.txt" --format 0 --diffsonly 1
```
```
24 configuration commands processed in simpleRTK2B_FW132_Rover_1Hz-00.txt

24 configuration commands processed in simpleRTK2B_FW132_Rover_10Hz-00.txt

2 files processed, list of differences in config keys and their values follows:

CFG_MSGOUT_NMEA_ID_GSA_UART1 (DIFFS!): {1: '1', 2: '0'}
CFG_MSGOUT_NMEA_ID_GSA_UART2 (DIFFS!): {1: '1', 2: '0'}
CFG_MSGOUT_NMEA_ID_GSV_UART1 (DIFFS!): {1: '1', 2: '0'}
CFG_MSGOUT_NMEA_ID_GSV_UART2 (DIFFS!): {1: '1', 2: '0'}
CFG_RATE_MEAS (DIFFS!): {1: '1000', 2: '100'}

Total config keys: 1475. Total differences: 5.
```

For help and full list of optional arguments, type:

```shell
ubxcompare -h
```

---
## <a name="troubleshooting">Troubleshooting</a>

1. `SPARTNTypeError` or `SPARTNParseError` when parsing encrypted messages with 16-bit gnssTimetags (`timeTagtype=0`), e.g. GAD or some OCB messages:

   ```
   pyspartn.exceptions.SPARTNTypeError: Error processing attribute 'group' in message type SPARTN-1X-GAD
   ```

   This is almost certainly due to an invalid decryption key and/or basedate. Remember that keys are only valid for a 4 week period, and basedates are valid for no more than half a day. Note also that different GNSS constellations use different UTC datums e.g. GLONASS timestamps are based on UTC+3. Check with your SPARTN service provider for the latest decryption key(s), and check the original creation date of your SPARTN datasource.

1. `SSL: CERTIFICATE_VERIFY_FAILED` error when attempting to connect to SPARTN MQTT service using `gnssmqttclient` on MacOS:

   ```
   [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1000)
   ```

   This is because `gnssmqttclient` is unable to locate the RootCA certificate for the MQTT Broker. This can normally be resolved as follows:
   - Install the latest version of certifi: ```python3 -m pip install --upgrade certifi```
   - Run the following command from the terminal (_substituting your Python path and version as required_): ```/Applications/Python\ 3.12/Install\ Certificates.command```


1. Unable to install `crytography` library required by `pyspartn` on 32-bit Linux platforms:

   ```
   Building wheel for cryptography (PEP 517): started
   Building wheel for cryptography (PEP 517): finished with status 'error'
   ```

   Refer to [cryptography installation README.md](https://github.com/semuconsulting/pyspartn/blob/main/cryptography_installation/README.md).


---
## <a name="gui">Graphical Client</a>

A python/tkinter graphical GPS client which utilises the `pygnssutils` library and supports NMEA, UBX, RTCM3 and NTRIP protocols is available at: 

[https://github.com/semuconsulting/PyGPSClient](https://github.com/semuconsulting/PyGPSClient)


---
## <a name="author">Author & License Information</a>

semuadmin@semuconsulting.com

![License](https://img.shields.io/github/license/semuconsulting/pygnssutils.svg)

`pygnssutils` is maintained entirely by unpaid volunteers. It receives no funding from advertising or corporate sponsorship. If you find the library useful, a small donation would be greatly appreciated!
