# Verification test catalog

## Common result fields

For every execution, copy the case into a dated result file and record hardware revision, firmware commit, source/load setup, instruments, raw readings, expected result, actual result and status.

## TC-START-001 - Safe startup

**Requirements:** SYS-ELEC-001, SYS-ELEC-005  
**Equipment:** 12 V current-limited source, small safe load, DMM  
**Procedure:**

1. Set the source to 12.0 V with a conservative current limit.
2. Connect the station and a small load.
3. Apply power while observing the load output.
4. Repeat from power-off and MCU-reset conditions.

**Pass:** The load is not energized before initialization checks complete; the station reaches `NORMAL` without uncontrolled switching.

## TC-MEAS-001 - Input-voltage accuracy

**Requirements:** SYS-MEAS-001, SYS-MEAS-004  
**Equipment:** Adjustable source and reference DMM  
**Points:** 9.0, 10.5, 12.0, 14.2 and 15.0 V, with protection action isolated or handled by the procedure.  
**Pass:** Absolute displayed error at every point is <=0.20 V after calibration.

## TC-MEAS-002 - Load-voltage accuracy

**Requirements:** SYS-MEAS-002, SYS-MEAS-004  
**Procedure:** Exercise representative output values including 0 V with switch off and normal loaded operation. Compare terminal voltage to the display/UART value.  
**Pass:** Absolute error at every declared point is <=0.20 V.

## TC-MEAS-003 - Load-current accuracy

**Requirements:** SYS-MEAS-003, SYS-MEAS-005  
**Equipment:** Current-limited 12 V source, load bank/electronic load, reference current measurement  
**Points:** 0.10, 0.25, 0.50, 0.90, 1.00 and 1.20 A.  
**Pass:** From 0.10 to 1.00 A, error is <=the greater of 0.05 A or 5% of reference; the measurement front end does not saturate at 1.20 A.

## TC-MEAS-004 - Voltage-drop calculation

**Requirements:** SYS-MEAS-007  
**Pass:** Reported drop equals reported `Vin - Vload` within numeric rounding and is consistent with DMM readings.

## TC-PATH-001 - One-ampere endurance and drop

**Requirements:** SYS-ELEC-003, SYS-ELEC-004, SYS-SAFE-003  
**Equipment:** Current-limited 12 V source, 1 A load, two voltage measurements or repeatable DMM method, temperature instrument  
**Procedure:**

1. Verify all component and connector ratings.
2. Operate at approximately 12 V and 1.0 A for 30 minutes.
3. Record `Vin`, `Vload`, current and observed temperatures at start and regular intervals.
4. Stop on any unsafe trend.

**Pass:** Operation remains controlled; station voltage drop is <=0.30 V after warm-up; no component exceeds its reviewed limit.

## TC-UVP-001 - Sustained under-voltage trip

**Requirements:** SYS-PROT-001  
**Equipment:** Adjustable source, timing instrument for final run  
**Procedure:** Begin at 12 V, then reduce below 10.5 V while recording `Vin`, switch command/output and state.  
**Pass:** A continuous low condition causes UVP trip after the configured confirmation interval and the load remains off.

## TC-UVP-002 - Under-voltage transient rejection

**Requirements:** SYS-PROT-002  
**Equipment:** Repeatable switched stimulus and timing instrument  
**Pass:** A below-threshold pulse shorter than 300 ms does not latch UVP; a sustained case still trips.

## TC-OVP-001 - Sustained over-voltage trip

**Requirements:** SYS-PROT-003  
**Equipment:** Adjustable current-limited source; do not exceed 15 V  
**Pass:** A continuous value above 14.2 V causes OVP trip after the configured interval; the load remains disconnected.

## TC-OCP-001 - Over-current warning

**Requirements:** SYS-PROT-004  
**Equipment:** Controlled load  
**Procedure:** Increase current above 0.90 A but below the trip threshold for more than 300 ms.  
**Pass:** The station indicates an OCP warning and continues controlled monitoring.

## TC-OCP-002 - Over-current trip

**Requirements:** SYS-PROT-005  
**Equipment:** Current-limited source, controlled load and timing instrument  
**Procedure:** Apply a controlled step to at least 1.20 A without a hard short.  
**Pass:** The station commands the switch off and enters latched OCP trip; final timing is evaluated in TC-TIME-001.

## TC-TIME-001 - Physical disconnect response

**Requirements:** SYS-PROT-005, SYS-VER-003  
**Equipment:** Oscilloscope or logic analyzer plus safe current/voltage observation  
**Measure:** Time from confirmed threshold crossing at the measurement/logic boundary to externally observable load disconnection.  
**Pass:** <=100 ms for the provisional OCP requirement. The exact trigger points and channels must be recorded.

## TC-RESET-001 - Latched trip

**Requirements:** SYS-PROT-006  
**Pass:** Returning the fault variable to normal does not automatically reconnect the load.

## TC-RESET-002 - Reset only after fault clear

**Requirements:** SYS-PROT-007  
**Pass:** Reset is rejected while a fault is active and accepted only after all monitored faults remain clear for at least 1 s.

## TC-RESET-003 - Intentional reset/debounce

**Requirements:** SYS-UI-004  
**Pass:** Contact bounce or a single short invalid edge does not clear the latch; the documented user action does.

## TC-UI-001 - State indication

**Requirements:** SYS-UI-001  
**Pass:** `STARTUP`, `NORMAL`, `WARNING` and `TRIPPED` are unambiguous during injected or physical cases.

## TC-UI-002 - Measurement display

**Requirements:** SYS-UI-002  
**Pass:** `Vin`, `Vload` and `Iload` appear with units and do not show stale normal values as valid during a trip or sensor error.

## TC-DIAG-001 - Trip-cause indication

**Requirements:** SYS-PROT-008, SYS-UI-003  
**Pass:** Separate UVP, OVP and OCP stimuli result in the correct retained cause.

## TC-DIAG-002 - UART evidence

**Requirements:** SYS-DIAG-001  
**Pass:** Logs contain measurement/state transitions needed to correlate the test without disrupting protection timing.

## Hard-short test status

No direct hard-short test case is authorized in this revision. Such a test may be added only after:

- fast protection topology and component energy limits are reviewed;
- a current-limited source and emergency disconnect are available;
- the exact objective and pass criterion are specified;
- the test is supervised under the course laboratory rules.
