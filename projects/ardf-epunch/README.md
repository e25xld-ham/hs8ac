# HS8AC ARDF ePunch

> Low-cost, offline-first electronic punching and real-time competition monitoring for Amateur Radio Direction Finding (ARDF), initiated by HS8AC / E25XLD in Thailand.

## Vision

HS8AC ARDF ePunch is an original low-cost modernization project for ARDF competition in Thailand. The goal is to replace manual paper punching/stamping with a reliable electronic system that amateur-radio associations can build, maintain and expand themselves.

The system is designed around one key principle:

**The field station must keep working even when the Internet does not.**

Real-time cloud reporting is an enhancement, not a dependency for recording a valid punch.

## Prototype V1

The first prototype uses only:

- 1 × START station
- 1 × FOX 1 station
- approximately 10 athlete NFC/RFID tags
- HS8AC cloud backend
- live monitoring at `ardf.hs8ac.com`

After field testing, the system can expand to:

- START
- FOX 1
- FOX 2
- FOX 3
- FOX 4
- FOX 5
- FINISH

Total: **7 independent stations**.

## Athlete Tag

Each athlete carries a passive 13.56 MHz NFC/RFID tag.

The tag:

- has no battery
- requires no charging
- can be reused
- can be made as a key-fob, wrist tag or competition badge
- is mapped to an athlete ID in the competition database

Example:

```text
Athlete: A025
Callsign: HS8XYZ
Tag UID: 04:A2:9C:72:D3:41:80
```

## Field Reader Architecture

Each START / FOX / FINISH module is an independent reader.

Prototype hardware target:

- ESP32-based controller
- 4G LTE modem
- PN532-class NFC reader for Prototype V1
- DS3231-class RTC
- local storage (microSD / flash)
- status LEDs
- buzzer
- battery power

Initial preferred integrated controller platform:

**LILYGO T-A7670G R2**

This combines ESP32 + LTE + SIM support + microSD capability, reducing wiring and failure points compared with using a basic Arduino board plus multiple external modules.

## Punch Flow

The reader must record the event locally before attempting cloud upload.

```text
Athlete tag
    |
    v
NFC reader detects UID
    |
    v
Read local RTC timestamp
    |
    v
Store punch locally FIRST
    |
    +--> Beep + green LED = accepted
    |
    v
Attempt 4G upload
    |
    +--> Server ACK -> mark synced
    |
    +--> No network -> keep in retry queue
```

A loss of 4G must **never** cause a punch to be lost.

## Timekeeping Rule

The official punch timestamp is the time at which the field reader detected the athlete tag.

It must **not** use the time at which the server received the HTTP/MQTT message, because cellular latency can vary.

Example event:

```json
{
  "event": "CHUMPHON_ARDF_2026",
  "station": "FOX01",
  "athlete": "A025",
  "tag_uid": "04A29C72D34180",
  "punch_time": "2026-12-05T10:42:16.384+07:00",
  "sequence": 1482,
  "signal": -83,
  "battery": 78
}
```

## Offline-First Reliability

Each reader must support:

- local event storage
- persistent sequence numbers
- restart recovery
- retry queue
- duplicate detection
- delayed upload after LTE returns
- server acknowledgement

The reader should remain usable while completely offline.

## Device Identity and Security

Every physical reader receives its own station identity and authentication secret.

Suggested station IDs:

```text
CP-START
CP-F01
CP-F02
CP-F03
CP-F04
CP-F05
CP-FINISH
```

Uploads must be authenticated/signed so that arbitrary clients cannot submit fake punches.

The backend must validate at least:

- competition/event ID
- device/station ID
- event sequence number
- timestamp
- tag UID
- message authentication/signature
- duplicate event ID

## Live Competition Dashboard

Target: `ardf.hs8ac.com`

Officials should eventually be able to see:

- athletes currently on course
- latest punches
- progress through FOX stations
- start time
- finish time
- preliminary elapsed time
- missing FOX stations
- duplicate/repeated punches
- delayed/offline-synced punches

Example:

```text
ATHLETE   START   F1   F2   F3   F4   F5   FINISH
HS8AAA      OK    OK   OK   OK   --   --     --
HS8BBB      OK    OK   --   OK   OK   --     --
```

## Device Health Dashboard

Each station should periodically send a heartbeat containing operational status such as:

- online/offline
- LTE signal strength
- battery voltage / percentage
- local storage status
- RTC status
- firmware version
- pending unsynced punch count
- last heartbeat

Example:

```text
FOX 1   ONLINE    LTE -79 dBm   Battery 87%   Queue 0
FOX 2   ONLINE    LTE -91 dBm   Battery 76%   Queue 0
FOX 3   OFFLINE   Last seen 3m  Battery 63%   Queue unknown
```

## Prototype Development Order

1. Power and USB communication
2. ESP32 firmware upload
3. NFC tag reading
4. RTC reading
5. local punch storage
6. LED/buzzer confirmation
7. 4G network connection
8. authenticated punch API
9. offline queue + retry
10. live web dashboard
11. outdoor field test
12. dedicated PCB / enclosure design

## Design Philosophy

The project should remain:

- low-cost
- reproducible
- modular
- repairable locally
- documented for beginners
- independent of continuous Internet access
- capable of growing from club-level use to larger competitions

The intention is not merely to build an IoT demonstration. The system should ultimately be suitable for real competition use after adequate engineering validation and field testing.

## Current Status

**Stage: Prototype planning / hardware acquisition**

Prototype V1 hardware is being selected and will be assembled first on a breadboard without soldering. Permanent soldering, enclosure design and PCB design will come only after the prototype circuit and firmware have been validated.

## Origin / Credits

Project initiated in 2026 by **E25XLD / HS8AC, Thailand**, with the goal of developing an accessible electronic punching platform for ARDF competition in Thailand.

System architecture and engineering development are being documented from the first prototype onward so the project's origin and evolution remain traceable.

---

**HS8AC — Chumphon Amateur Radio Society**
