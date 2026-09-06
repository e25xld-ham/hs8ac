# Development Roadmap

## Phase 0 — Definition

Status: **in progress**

- Define system goals
- Define offline-first requirement
- Define Start / Fox / Finish station roles
- Define athlete tag concept
- Select Prototype V1 hardware family
- Publish architecture and project origin

## Phase 1 — Bench Prototype

Target hardware:

- LILYGO T-A7670G R2
- PN532 NFC reader
- DS3231 RTC
- microSD
- 10 passive NFC/RFID tags
- buzzer + LEDs

Milestones:

1. ESP32 boots reliably
2. firmware uploads from development computer
3. reader detects 10/10 test tags repeatedly
4. RTC is readable and configurable
5. local punch event is written persistently
6. local success feedback works
7. reboot does not lose queued punches
8. LTE connection works with Thai mobile SIM
9. authenticated test event reaches HS8AC backend

## Phase 2 — Offline Queue Validation

Tests:

- remove SIM and punch several tags
- disable LTE during punches
- reboot with unsynced events
- restore LTE and verify complete replay
- intentionally duplicate network delivery
- verify server idempotency
- verify no locally accepted punch disappears

Acceptance condition:

**Every locally confirmed test punch must remain recoverable after network loss and restart.**

## Phase 3 — Start + Fox 1 Field Prototype

Deploy two independent stations:

- CP-START
- CP-F01

Use approximately 10 athlete tags.

Measure:

- tag reading speed
- usability while running
- accidental/double touch behavior
- LTE performance
- battery endurance
- clock drift
- outdoor heat/humidity behavior
- recovery from poor cellular signal

## Phase 4 — HS8AC Cloud + Live Dashboard

Integrate with `ardf.hs8ac.com`.

Officials should see:

- athlete registration/tag mapping
- live start events
- live Fox 1 events
- latest punch feed
- preliminary elapsed time
- device online/offline state
- signal/battery status
- delayed sync markers
- audit/event logs

## Phase 5 — Engineering Hardening

After successful field prototype:

- review PN532 vs newer production NFC reader options
- finalize electrical design
- define production connectors
- improve surge/ESD/power protection
- improve battery system
- design waterproof enclosure
- design maintenance/service procedure
- define firmware update process
- define device provisioning/key rotation

## Phase 6 — Custom PCB

Only after pin assignments, power design and firmware interfaces are stable.

Goals:

- reduce loose wiring
- reduce assembly mistakes
- standardize every station
- simplify repair
- document test points
- create repeatable HS8AC hardware revision numbers

Example revision naming:

```text
HS8AC-EPUNCH-READER Rev.A
HS8AC-EPUNCH-READER Rev.B
```

## Phase 7 — Full Competition Set

Build seven independently configured stations:

```text
START
FOX 1
FOX 2
FOX 3
FOX 4
FOX 5
FINISH
```

Run simulated and real-course validation before any official scoring dependency.

## Phase 8 — Documentation and Reproducibility

Publish enough documentation for another amateur-radio team to reproduce the platform safely:

- BOM
- wiring diagrams
- pinout tables
- firmware build instructions
- server/API specification
- deployment guide
- field setup checklist
- competition-day checklist
- troubleshooting guide
- test procedures

Sensitive credentials and production secrets must not be published.

## Long-term Direction

The long-term objective is a practical, low-cost ARDF electronic punching platform originating from HS8AC in Thailand, capable of being adapted by other associations without requiring expensive proprietary timing infrastructure.
