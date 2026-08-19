# AVIONIX-28
AVIONIX-28 Multi-Rail Power Supply Module for MIL-STD-704 Avionics Bus  A sequenced, EMI-filtered power conversion board that takes a 28V DC avionics bus input and produces regulated 5V, 3.3V, and 1.8V rails suitable for powering downstream digital/mixed-signal avionics electronics.

# AVIONIX-28
### Multi-Rail Power Supply Module for MIL-STD-704 Avionics Bus

A sequenced, EMI-filtered power conversion board that takes a 28V DC avionics
bus input and produces regulated 5V, 3.3V, and 1.8V rails suitable for
powering downstream digital/mixed-signal avionics electronics.

---

## 1. Overview

Every avionics subsystem — flight computers, sensor interface boards, radios,
display drivers — needs clean, sequenced, low-noise power derived from the
aircraft's primary 28V DC bus. This project implements that power stage as a
standalone module, designed against the electrical characteristics defined in
**MIL-STD-704** (Aircraft Electric Power Characteristics).

This is a learning/portfolio project intended to demonstrate practical power
electronics design skills relevant to defense and aerospace electronics work:
wide-input buck regulation, power sequencing, and EMI-aware layout.

## 2. Key Features

- **Input**: 28V DC nominal, designed to tolerate MIL-STD-704 transient
  conditions (18V–50V short-duration excursions, steady-state 22–29V)
- **Outputs**: 5V, 3.3V, 1.8V regulated rails
- **Sequencing**: Power-Good–gated startup order (5V → 3.3V → 1.8V)
- **Protection**: Reverse polarity protection, TVS transient clamping,
  overcurrent protection
- **EMI filtering**: Common-mode choke + differential filtering on the input
  stage, designed with MIL-STD-461 conducted emissions practices in mind

## 3. Design Reference: MIL-STD-704

| Parameter | Spec |
|---|---|
| Nominal bus voltage | 28V DC |
| Normal steady-state range | ~22V – 29V DC |
| Undervoltage transient | down to ~18V (short duration) |
| Overvoltage transient | up to ~50V (short duration) |
| Ripple | up to 1.5V p-p |

## 4. Power Rail Application Map

| Rail   | Voltage | Typical Consumers                                              | Logic Family     | Function on Board                                              |
|--------|---------|------------------------------------------------------------------|-------------------|------------------------------------------------------------------|
| Rail 1 | 5.0V    | RS-422/RS-485 transceiver, op-amp/comparator, ADC analog supply | TTL / 5V CMOS     | Legacy interface signaling to other avionics units, analog sensor conditioning |
| Rail 2 | 3.3V    | MCU (I/O + peripherals), MIL-STD-1553/ARINC429 transceiver, Flash/EEPROM | LVCMOS / LVTTL    | Main digital logic, bus communication, non-volatile storage      |
| Rail 3 | 1.8V    | Precision ADC reference / analog front-end (or reserved for expansion) | LVCMOS 1.8V       | Low-noise reference rail for precision analog, or provisioned future use |


The input protection stage is designed to survive these conditions without
damage or latch-up, and to maintain regulation through normal operating
excursions.

## 5. Architecture

```
28V Bus
  │
  ▼
Reverse Polarity Protection
  │
  ▼
TVS Transient Clamp
  │
  ▼
EMI Input Filter (CM choke + DM inductor + bulk/ceramic caps)
  │
  ▼
┌─────────────┐     PG      ┌─────────────┐     PG      ┌─────────────┐
│  5V Buck    │────────────▶│  3.3V Ldo  │────────────▶│  1.8V LDO   │
│  Converter  │   enables   │    │        │             │
└─────────────┘             └─────────────┘             └─────────────┘
     │                            │                            │
     ▼                            ▼                            ▼
  5V Rail                     3.3V Rail                    1.8V Rail
```

Each regulator's Power Good (PG) output gates the Enable pin of the next
stage, ensuring rails come up only after the previous rail is confirmed
stable — rather than relying on a fixed timer delay.

## 6. Bill of Materials (Draft — TBD)

*(To be finalized after LTspice simulation and component selection.)*




## 7. Reference articles and resources used in this  design:
- Analog.com Multirail_Powersupply Design part1: https://www.analog.com/en/resources/analog-dialogue/articles/multirail-power-supply-design-for-successful-application-boards-part1.html  ,
- part2: https://www.analog.com/en/resources/analog-dialogue/articles/multirail-power-supply-design-for-successful-application-boards-part2.html
- Esd Protection: https://youtu.be/MmG_m4xVNfQ?si=btMzvDi-hvi9JBXi
- buck ic calculations : https://www.ti.com/lit/an/slva477b/slva477b.pdf?ts=1787028919406&ref_url=https%253A%252F%252Fwww.google.com%252F
## 8. Disclaimer

This is an educational/portfolio project. It follows MIL-STD-704 and
MIL-STD-461 design *principles* for learning purposes but has not undergone
formal qualification or certification testing, and is not intended for use
in actual flight hardware.
