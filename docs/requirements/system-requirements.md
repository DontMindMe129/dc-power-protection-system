# System requirements

## 1. Requirement conventions

- **Selected**: agreed baseline for the present project phase.
- **Provisional**: numeric target used to assess feasibility; must be confirmed after component selection and prototype characterization.
- **TBD**: intentionally not yet specified.
- "Shall" denotes a mandatory requirement.

Changes to requirement IDs are discouraged. If a requirement is removed, mark it retired rather than reusing the ID.

## 2. Operating baseline

| Parameter | Value | Status |
| --- | ---: | --- |
| Nominal input voltage | 12.0 V DC | Selected |
| Controlled measurement/protection range | 9.0-15.0 V DC | Selected |
| Maximum continuous load current | 1.0 A | Selected |
| Default UVP threshold | 10.5 V | Provisional |
| UVP confirmation time | 500 ms | Provisional |
| Default OVP threshold | 14.2 V | Provisional |
| OVP confirmation time | 100 ms | Provisional |
| OCP warning threshold | 0.90 A | Provisional |
| OCP warning confirmation time | 300 ms | Provisional |
| OCP trip threshold | 1.20 A | Provisional |
| OCP disconnect time | <=100 ms after confirmed threshold crossing | Provisional |
| Fault-clear dwell before manual reset is accepted | 1 s | Provisional |

The protection thresholds are deliberately configurable in firmware. Final defaults depend on the selected source/load scenario, sensor accuracy and safe operating area of the switch.

## 3. Electrical and power-path requirements

| ID | Requirement | Status | Verification |
| --- | --- | --- | --- |
| SYS-ELEC-001 | The station shall accept a nominal 12 V DC source through `DC IN`. | Selected | Inspection + test |
| SYS-ELEC-002 | The station shall remain controlled throughout an applied input range of 9.0-15.0 V DC; an out-of-normal voltage may cause a controlled trip. | Selected | Test |
| SYS-ELEC-003 | The station power path shall carry 1.0 A continuously for 30 minutes in the declared test environment. | Selected | TC-PATH-001 |
| SYS-ELEC-004 | The total `Vin - Vload` drop introduced by the station at 1.0 A shall be <=0.30 V after warm-up. | Provisional | TC-PATH-001 |
| SYS-ELEC-005 | The station shall default to a non-energized load output during reset until initialization and initial measurement checks complete. | Provisional | TC-START-001 |
| SYS-ELEC-006 | Loss or reset of the MCU shall not defeat the dedicated fast-current protection path. | Selected | Inspection + analysis |
| SYS-ELEC-007 | A destructive-current fault shall be limited by a hardware mechanism independent of normal firmware execution. | Selected | Design review; controlled fault test only after safety gate |

## 4. Measurement requirements

| ID | Requirement | Status | Verification |
| --- | --- | --- | --- |
| SYS-MEAS-001 | The station shall measure `Vin` from 9.0 to 15.0 V. | Selected | TC-MEAS-001 |
| SYS-MEAS-002 | The station shall measure `Vload` from 0 to 15.0 V. | Selected | TC-MEAS-002 |
| SYS-MEAS-003 | The station shall measure `Iload` from 0 to at least 1.20 A without ADC saturation. | Provisional | TC-MEAS-003 |
| SYS-MEAS-004 | After calibration, displayed `Vin` and `Vload` error shall be <= +/-0.20 V at the specified test points. | Provisional | TC-MEAS-001/002 |
| SYS-MEAS-005 | After calibration, displayed `Iload` error from 0.10 to 1.00 A shall be <= the greater of +/-0.05 A or +/-5% of reference. | Provisional | TC-MEAS-003 |
| SYS-MEAS-006 | The protection algorithm shall use filtered measurements and shall document its sample period, filter and confirmation timing. | Selected | Inspection + firmware test later |
| SYS-MEAS-007 | The station shall calculate and expose the voltage drop `Vin - Vload`. | Provisional | TC-MEAS-004 |

## 5. Protection behavior

| ID | Requirement | Status | Verification |
| --- | --- | --- | --- |
| SYS-PROT-001 | If `Vin < 10.5 V` continuously for 500 ms, the station shall enter a UVP trip state and command the load switch off. | Provisional | TC-UVP-001 |
| SYS-PROT-002 | A transient below the UVP threshold lasting less than 300 ms shall not cause a UVP trip. | Provisional | TC-UVP-002 |
| SYS-PROT-003 | If `Vin > 14.2 V` continuously for 100 ms, the station shall enter an OVP trip state and command the load switch off. | Provisional | TC-OVP-001 |
| SYS-PROT-004 | If `Iload > 0.90 A` continuously for 300 ms, the station shall present an over-current warning without necessarily disconnecting the load. | Provisional | TC-OCP-001 |
| SYS-PROT-005 | If `Iload >= 1.20 A`, the station shall command load disconnection within 100 ms of the confirmed threshold crossing. | Provisional | TC-OCP-002 |
| SYS-PROT-006 | Once any UVP, OVP or OCP trip occurs, the trip state shall remain latched when the measured value returns to normal. | Selected | TC-RESET-001 |
| SYS-PROT-007 | The station shall accept manual reset only after all monitored trip conditions remain clear for at least 1 s. | Provisional | TC-RESET-002 |
| SYS-PROT-008 | The station shall record or display the primary cause of the most recent trip. | Selected | TC-DIAG-001 |
| SYS-PROT-009 | Firmware protection shall not be claimed as the sole protection against a hard short circuit. | Selected | Inspection |

## 6. User-interface and diagnostics requirements

| ID | Requirement | Status | Verification |
| --- | --- | --- | --- |
| SYS-UI-001 | The local UI shall distinguish at least `STARTUP`, `NORMAL`, `WARNING` and `TRIPPED`. | Selected | TC-UI-001 |
| SYS-UI-002 | The display shall show `Vin`, `Vload` and `Iload` in engineering units during normal operation. | Selected | TC-UI-002 |
| SYS-UI-003 | A trip indication shall identify UVP, OVP or OCP rather than showing only a generic failure. | Selected | TC-DIAG-001 |
| SYS-UI-004 | A reset action shall require an intentional button operation and shall not occur from a single un-debounced edge. | Provisional | TC-RESET-003 |
| SYS-DIAG-001 | UART diagnostics shall expose state transitions and measured values sufficient to support test evidence. | Provisional | TC-DIAG-002 |

## 7. Safety and verification requirements

| ID | Requirement | Status | Verification |
| --- | --- | --- | --- |
| SYS-SAFE-001 | Powered development tests shall use a source with known current limiting or an equivalent series protection device. | Selected | Procedure audit |
| SYS-SAFE-002 | A direct short across `DC OUT` shall not be performed with an uncontrolled fixed adapter. | Selected | Procedure audit |
| SYS-SAFE-003 | Components in the 1 A path shall be selected with documented voltage, current, power and thermal margin. | Selected | Design review |
| SYS-SAFE-004 | The test setup shall include a readily accessible means to remove input power. | Selected | Pre-test checklist |
| SYS-VER-001 | Every mandatory system requirement shall have a verification method before detailed design is approved. | Selected | Traceability review |
| SYS-VER-002 | Every executed test shall record hardware revision, firmware revision, setup, expected result, actual result and pass/fail disposition. | Selected | Result review |
| SYS-VER-003 | Protection response-time claims shall be verified with an oscilloscope, logic analyzer or equivalent timing instrument at least once on the integrated prototype. | Selected | TC-TIME-001 |

## 8. Requirements intentionally left TBD

- Maximum allowed PCB/component temperature rise at 1 A.
- Absolute maximum non-operating input voltage.
- Connector current rating and mechanical retention.
- Reverse-polarity behavior.
- Input surge/transient level.
- OLED update rate and UART baud rate.
- Long-term measurement drift.

These values depend on detailed hardware selection. They must be resolved before schematic sign-off, not guessed during the requirements phase.
