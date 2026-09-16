# Initial feasibility assessment

## Verdict

**Conditionally feasible for a course project.** The 12 V / 1 A scope is technically moderate and supports a balanced hardware-firmware project. The present blocker is not the MCU workload; it is obtaining safe, repeatable test stimulus and one session of timing/thermal measurement equipment.

The project should proceed if the team accepts the minimum test-resource plan below. If the team cannot access any adjustable/current-limited source or timing instrument, the project can still be demonstrated, but several protection claims cannot be honestly verified.

## Feasibility by area

| Area | Assessment | Reason |
| --- | --- | --- |
| Electrical power level | Feasible | 12 V at 1 A is 12 W delivered to an external load; the station should dissipate only a small fraction with suitable MOSFET, shunt and copper sizing. |
| Voltage measurement | Feasible | A divided and protected signal is compatible with an MCU ADC; calibration against a DMM is practical. |
| Current measurement | Feasible | A shunt/current-sense front end for 0-1.2 A is within common low-voltage design practice. |
| Firmware | Feasible | Acquisition, filtering, thresholds, state machine, UI and UART fit comfortably within an STM32F103-class MCU. |
| UVP/OVP test | Conditional | Requires a safely adjustable 9-15 V source; a fixed 12 V adapter cannot create both thresholds repeatably. |
| OCP static test | Conditional | Requires a load bank/electronic load capable of approximately 0.1-1.2 A. |
| Response-time verification | Blocked with current tools | A multimeter cannot validate a <=100 ms disconnect claim; one oscilloscope or logic-analyzer session is required. |
| Hard-short validation | High risk if improvised | Must use current-limited equipment after hardware protection review; not a first-stage functional test. |
| PCB integration | Feasible | 1 A is manageable, but connector rating, copper width, shunt heating and MOSFET SOA must be reviewed. |

## Minimum additional test access

The team does not need to own a complete laboratory, but it should arrange access to:

1. An adjustable DC source covering 9-15 V with a known current limit, or a supervised lab equivalent.
2. A safe resistive load bank or electronic load reaching at least 1.2 A at 12 V.
3. A reference DMM.
4. An oscilloscope or logic analyzer for at least the final trip-timing test.
5. A way to observe temperature during the 1 A endurance test; even a borrowed contact thermometer is better than touch-based judgment.

If a resistor is used as the 1 A nominal load, an approximate value is 12 ohm and it dissipates about 12 W. A part rated comfortably above this dissipation, such as 25 W, is required and will become hot. At 1.2 A and 12 V, a 10 ohm load dissipates about 14.4 W.

## What can be done immediately with basic tools

- Requirement and architecture review.
- Divider/shunt calculations and tolerance analysis.
- Circuit simulation for sensing and switching.
- Firmware state-machine tests using injected ADC values.
- UI and UART behavior tests.
- Static checks at one or a few safe operating points using a DMM and appropriate load.

## What cannot be claimed yet

- Verified <=100 ms physical disconnect time.
- Safe hard-short interruption.
- Final measurement accuracy across the entire range.
- Continuous 1 A thermal performance.
- Safe operation at input levels outside 9-15 V.

## Principal risks and mitigations

| Risk | Consequence | Mitigation / exit criterion |
| --- | --- | --- |
| No current-limited adjustable source | Unsafe or non-repeatable UVP/OVP/OCP tests | Reserve supervised lab access before schematic sign-off |
| Load resistor undersized | Burn hazard or invalid current point | Calculate dissipation; use >=2x practical power margin and guarded placement |
| Firmware treated as short-circuit protection | MOSFET/shunt damage before MCU reacts | Independent fuse/current limit/comparator path; review before fault test |
| Sensor offset/tolerance | False trips or inaccurate display | Calibration points, tolerance budget and configurable thresholds |
| MOSFET selected only by current rating | Excess heating or unsafe fault behavior | Check `RDS(on)`, gate drive, SOA, fault energy and PCB copper |
| Requirements change without updating tests | Unverifiable final report | Maintain stable IDs and traceability matrix in each review |

## Go/no-go checkpoint

Proceed to detailed architecture if all answers are **yes**:

- [ ] Can the team obtain an adjustable/current-limited source before integration testing?
- [ ] Can the team obtain or build a safely rated load bank up to 1.2 A?
- [ ] Can the team borrow a scope or logic analyzer for one timing session?
- [ ] Will the hardware include a protection mechanism independent of normal firmware execution?
- [ ] Will the team avoid direct-short testing until the safety gate is passed?

If the first three remain **no**, reduce the claims to a monitor plus controlled load disconnect demonstration, or change to a project with simpler test stimulus.
