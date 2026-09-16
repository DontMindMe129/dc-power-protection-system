# Test specification

## 1. Purpose

This specification defines how the proposed requirements can be verified before the detailed design is accepted. It covers inspection, analysis, simulation and physical test.

## 2. Verification methods

| Method | Use |
| --- | --- |
| Inspection | Check schematic, source, ratings, configuration or visible behavior |
| Analysis | Calculate tolerance, power, timing or thermal margin |
| Simulation | Explore sensing/switching behavior before hardware is available |
| Test | Apply controlled stimulus and compare observed behavior with acceptance criteria |

Simulation supports design confidence but does not replace integrated hardware tests for measurement accuracy, switching delay or thermal behavior.

## 3. Test levels

1. **T0 - Documentation review:** requirement quality, traceability and safety gates.
2. **T1 - Host/firmware logic:** injected measurements exercise thresholds, debounce, state and reset rules.
3. **T2 - Low-energy circuit test:** sensing and switch-control behavior with current limiting.
4. **T3 - Integrated nominal test:** 12 V operation up to 1 A.
5. **T4 - Controlled fault test:** UVP/OVP/OCP using adjustable/current-limited equipment.
6. **T5 - Final timing and thermal verification:** scope/logic analyzer and temperature measurement.

## 4. Equipment classes

| Class | Equipment | Supported claims |
| --- | --- | --- |
| Basic | DMM, fixed adapter, known resistors/load, UART terminal | Visual/UI tests, limited static accuracy checks |
| Intermediate | Adjustable regulated source with current limit, load bank, DMM | Threshold sweeps, static OCP and power-path checks |
| Full | Intermediate plus oscilloscope/logic analyzer and temperature instrument | Trip timing, transient rejection and thermal evidence |

The selected project currently has Basic access. Intermediate access is a project prerequisite before integrated fault testing; Full access is needed at least once before final acceptance.

## 5. General acceptance rules

- Test values shall be measured at the station terminals, not assumed from adapter labels.
- Threshold tests shall approach the threshold from both directions where applicable.
- A provisional requirement may pass provisionally, but cannot become final until its value is reviewed against component tolerance and test uncertainty.
- Any unexpected reset, uncontrolled switching or component overheating is an immediate stop condition.
- A failed test is recorded; the result is not deleted after a fix. The rerun references the earlier failure.
- A test marked `BLOCKED` is not equivalent to `PASS`.

## 6. Required result metadata

Every result record shall include:

- test-case ID and requirement IDs;
- date and operator;
- hardware revision and firmware commit;
- schematic of the test connection or clear photo;
- equipment identifiers/ranges;
- initial conditions and applied stimulus;
- expected and actual result;
- raw measurements where applicable;
- pass/fail/blocked status;
- anomaly and follow-up notes.

## 7. Safety gates

- No T3-T5 test without a reviewed power-path schematic.
- No test above 0.2 A on solderless breadboard power rails unless the path and contacts are explicitly evaluated.
- No direct output short from an uncontrolled fixed adapter.
- OVP tests stop at 15 V until the non-operating absolute maximum is specified.
- A hard-short test requires an independent current limit, fast hardware protection and an emergency power disconnect.
