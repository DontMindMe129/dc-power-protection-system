# Verification test plan

## 1. Objective

Demonstrate that the proposed system is safe enough to prototype, measurable with available or borrowable equipment, and capable of satisfying each mandatory V1 requirement.

## 2. Strategy

Tests progress from low-energy and simulated stimuli to integrated 12 V / 1 A operation. Fault energy is increased only after the previous stage passes.

```text
Requirements review
        -> calculation and simulation
        -> firmware logic with injected values
        -> low-energy sensing/switch tests
        -> nominal integrated test
        -> controlled UVP/OVP/OCP tests
        -> timing and thermal verification
```

## 3. Entry criteria

| Stage | Entry criteria |
| --- | --- |
| Documentation review | Requirement IDs and owners exist |
| Firmware logic test | State and threshold rules are defined |
| Low-energy hardware | Schematic review complete; current limit active |
| Nominal 1 A test | PCB/current path inspected; static low-current tests pass |
| Fault testing | Fast protection reviewed; emergency disconnect available |
| Final timing/thermal | Stable integrated prototype; suitable instruments booked |

## 4. Exit criteria

- All selected requirements have `PASS` evidence or an explicitly accepted deviation.
- Provisional thresholds have been confirmed or changed through review.
- No unresolved safety-critical failure remains.
- Required reports contain raw data and revision information.
- Traceability matrix matches the final requirement and test revisions.

## 5. Planned sequence

| Order | Test group | Equipment class | Purpose |
| ---: | --- | --- | --- |
| 1 | Documentation/safety review | Basic | Remove contradictions and unsafe procedures |
| 2 | Injected-value protection logic | Basic | Prove FSM, threshold, debounce and latch logic |
| 3 | Static voltage sensing | Intermediate | Calibrate `Vin` and `Vload` |
| 4 | Static current sensing | Intermediate | Calibrate `Iload` |
| 5 | Startup/UI/reset | Intermediate | Verify integrated behavior at low energy |
| 6 | 1 A path endurance | Full preferred | Verify drop and heating |
| 7 | UVP/OVP/OCP thresholds | Intermediate | Verify protection transitions |
| 8 | Response timing/transients | Full | Verify timing and rejection |
| 9 | Controlled hardware fault test | Full, gated | Verify independent protection if required |

## 6. Resource plan for the selected Basic setup

### Work that can start without borrowing equipment

- Requirement and traceability review.
- Firmware threshold/state tests with injected values.
- UI and UART tests.
- Simulation and tolerance calculations.
- Preparation of load bank and guarded test harness.

### Equipment that must be borrowed or arranged

- Adjustable current-limited source for 9-15 V.
- Load bank/electronic load to at least 1.2 A.
- Oscilloscope or logic analyzer for response time.
- Temperature instrument for the endurance test.

## 7. Stop conditions

Immediately remove power when any of the following occurs:

- unexpected smoke, odor, arcing or audible instability;
- connector, wire, resistor, shunt or switch temperature rising unexpectedly;
- MCU resets repeatedly under load;
- load output cannot be disabled;
- supply current limit engages outside the intended case;
- measured voltage exceeds the approved test envelope.
