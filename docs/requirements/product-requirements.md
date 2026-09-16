# Product requirements

## 1. Purpose

The product is a low-voltage DC monitoring and protection station placed between an external source and an external load. It provides visibility into the electrical state of the path and disconnects the load when an abnormal voltage or current condition is confirmed.

This document describes user and product needs. Detailed measurable statements are maintained in `system-requirements.md`.

## 2. Problem statement

A simple DC source-load connection gives the user little information about input voltage, load voltage, load current or the reason a load stopped operating. It may also leave the load connected during an abnormal condition. The proposed product adds measurement, indication and controlled disconnection without designing the external source or the external load.

## 3. Stakeholders

| Stakeholder | Need |
| --- | --- |
| User/operator | See present electrical values and protection state |
| User/operator | Receive a warning before or when an abnormal condition occurs |
| User/operator | Safely reset the station after the cause of a trip is removed |
| Development team | Demonstrate balanced hardware, firmware, integration and testing work |
| Course assessor | Trace requirements to design evidence and test results |

## 4. System boundary

### Inside the project

- DC input and output connectors.
- Input protection required for the selected operating envelope.
- Voltage and current sensing.
- Controllable load switch.
- MCU, local power supply and programming/debug interface.
- Local display, buttons and audible/visual indication.
- Firmware for acquisition, filtering, protection state management, UI and diagnostics.
- The PCB power path from `DC IN` to `DC OUT`.

### Outside the project

- The external DC source.
- The external load.
- The physical cable/path after `DC OUT` unless a dedicated test cable is documented.
- Design of a battery charger, bench supply or load itself.
- Mains-voltage switching.

## 5. Baseline use environment

| Parameter | Baseline | Status |
| --- | --- | --- |
| Nominal source | 12 V DC | Selected for feasibility |
| Evaluation range | 9-15 V DC | Selected for feasibility |
| Continuous load current | Up to 1.0 A | Selected for feasibility |
| Indoor laboratory use | Dry, supervised environment | Assumption |
| Input source behavior | Regulated DC source or equivalent test source | Assumption |

The 9-15 V range is the range over which the station must remain controlled and measure/protect the path. It is not the default "normal" voltage window. The default UVP/OVP thresholds are narrower and provisional.

## 6. Product-level needs

| ID | Product need |
| --- | --- |
| PR-01 | The product shall show the user whether the load is connected, warned or tripped. |
| PR-02 | The product shall measure and display `Vin`, `Vload` and `Iload`. |
| PR-03 | The product shall detect sustained under-voltage, over-voltage and over-current conditions. |
| PR-04 | The product shall disconnect the external load when a trip condition is confirmed. |
| PR-05 | A protection trip shall remain latched until the fault clears and the user requests a reset. |
| PR-06 | Short-circuit energy shall be limited by dedicated hardware rather than relying only on MCU software. |
| PR-07 | The product shall provide sufficient diagnostic information to identify the trip cause. |
| PR-08 | The product shall be testable without intentionally exposing the MCU or operator to uncontrolled fault energy. |
| PR-09 | Requirements, design decisions and results shall be traceable in the repository. |

## 7. V1 scope

### Included

- Input/load voltage measurement.
- Load-current measurement.
- Under-voltage protection (UVP).
- Over-voltage protection (OVP).
- Over-current protection (OCP).
- Warning, trip, latched state and manual reset behavior.
- OLED/status indication and UART diagnostic output.
- Hardware fast protection for destructive current faults.
- Calibration and basic measurement-error characterization.

### Deferred

- Over-temperature protection.
- Energy metering with billing-grade accuracy.
- Wireless/IoT connectivity.
- Automatic reconnection after a trip.
- Mains AC operation.
- Arbitrary source and load compatibility.

## 8. Product acceptance gates

The V1 prototype is acceptable only when:

1. Static voltage and current measurement requirements pass.
2. UVP, OVP and OCP behavior passes for threshold, debounce and latch/reset behavior.
3. The 1 A power-path test completes without unsafe heating or loss of control.
4. A temporary lab setup verifies the externally observable trip response time.
5. No uncontrolled direct-short test is required to claim basic firmware functionality.
6. Every failed requirement is documented with evidence and disposition.

## 9. Open product decisions

- Exact load-switch topology and component.
- Current-sense topology and shunt value.
- Input reverse-polarity and surge strategy.
- Exact PCB form factor and connector family.
- Whether a temperature sensor/OTP is added after the V1 feasibility review.
- Final course deadline, team roles and submission format.
