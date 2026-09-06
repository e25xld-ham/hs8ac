# Prototype V1 — Bill of Materials (BOM)

This BOM is intentionally limited to one bench prototype set. Do **not** buy seven complete stations yet.

## Core electronics

| Item | Qty | Purpose | Notes |
|---|---:|---|---|
| LILYGO T-A7670G R2 | 1 | ESP32 + 4G LTE controller | Preferred prototype controller |
| PN532 NFC module | 1 | Athlete tag reader | Prototype V1 reader |
| DS3231 RTC module | 1 | Local competition timestamp | Use battery-backed RTC |
| CR2032 cell | 1 | RTC backup battery | Check correct module battery support |
| microSD 16–32 GB | 1 | Local punch/event storage | High-endurance card preferred later |
| Passive NFC/RFID tags, 13.56 MHz, ISO14443A | 10 | Athlete tags | Use inexpensive generic tags for testing |
| Active buzzer, 3.3 V compatible | 2 | Audible punch feedback | One spare |
| Green 5 mm LEDs | 3 | Successful punch / status | One used initially |
| Red 5 mm LEDs | 3 | Fault / reject indication | One used initially |
| 220–330 ohm resistors | 10 | LED current limiting | Required with LEDs |

## Prototyping accessories

| Item | Qty | Purpose |
|---|---:|---|
| Medium breadboard | 1 | Solderless prototype wiring |
| Dupont male-to-male wire set | 1 | Breadboard connections |
| Dupont male-to-female wire set | 1 | Module connections |
| USB-C data cable | 1 | Programming and bench power |
| Nano-SIM with mobile data | 1 | LTE testing |
| USB power bank | 1 | Portable test power |
| Digital multimeter | 1 | Voltage, continuity and short-circuit checks |

## Tools for later permanent assembly

These are not required for the first no-solder bench test, but will be needed later:

- temperature-controlled soldering iron, approximately 40–60 W
- electronics solder
- flux
- soldering stand
- side cutters
- wire stripper
- small screwdrivers
- heat-shrink tubing
- helping-hands / PCB holder

## Do not buy yet

Wait until Prototype V1 passes bench and field tests before purchasing:

- all seven controller boards
- custom PCB
- waterproof competition enclosure
- large battery packs
- custom athlete tags
- external LTE antennas beyond what is needed for initial testing
- production cabling/connectors

## Procurement rule

Before purchasing a board or module that has multiple revisions, verify the exact product photo/model/revision. Similar product names can have different LTE modems, pinouts or voltage requirements.

## Prototype target

The first hardware milestone is:

```text
ESP32 boots
 -> computer can upload firmware
 -> NFC tag UID is read
 -> RTC timestamp is read
 -> event is written locally
 -> buzzer/LED confirms punch
 -> LTE connects
 -> event reaches HS8AC server
```
