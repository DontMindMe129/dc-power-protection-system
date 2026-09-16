# Use cases

## UC-01 - Normal monitoring

**Actor:** Operator  
**Precondition:** A valid source and load are connected; no trip is latched.  
**Main flow:**

1. The station initializes with the load output disabled.
2. Initial measurements are checked.
3. The station enables the load output.
4. The station periodically measures `Vin`, `Vload` and `Iload`.
5. The UI shows the measurements and `NORMAL` state.

**Success condition:** The load remains powered and measurements remain within specified error limits.

## UC-02 - Sustained under-voltage

**Trigger:** `Vin` remains below the UVP threshold for the configured confirmation interval.

1. The station detects the condition using filtered measurements.
2. The station commands the load switch off.
3. The state becomes `TRIPPED_UVP`.
4. The UI and UART identify UVP as the cause.
5. Restoring voltage alone does not reconnect the load.

## UC-03 - Sustained over-voltage

Same general flow as UC-02, with the OVP threshold and `TRIPPED_OVP` cause.

## UC-04 - Over-current warning and trip

1. A sustained current above the warning threshold causes `WARNING_OCP`.
2. The station continues monitoring.
3. If current reaches the OCP trip threshold, the station commands disconnection.
4. The state becomes `TRIPPED_OCP` and remains latched.

The fast hardware path remains responsible for limiting a hard short that develops faster than firmware can safely handle.

## UC-05 - Manual recovery

**Precondition:** A trip is latched.

1. The operator removes the cause of the fault.
2. The station observes normal values for the fault-clear dwell interval.
3. The operator intentionally requests reset.
4. The station clears the latch, rechecks measurements and reconnects the load only if safe.

**Alternate flow:** A reset request while the fault remains present is rejected and the load stays disconnected.

## UC-06 - Development diagnostics

The developer connects UART and observes measurement samples, state transitions and trip cause while running a documented test case. UART is supporting evidence; it does not replace an external timing measurement for protection-response claims.
