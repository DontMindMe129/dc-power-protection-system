# Requirements traceability matrix

Status values: `PLANNED`, `BLOCKED`, `PASS`, `FAIL`, `NOT RUN`.

| Requirement | Primary verification | Current status | Blocking resource or decision |
| --- | --- | --- | --- |
| SYS-ELEC-001 | Inspection + TC-START-001 | NOT RUN | Hardware not built |
| SYS-ELEC-002 | TC-MEAS-001, TC-UVP-001, TC-OVP-001 | BLOCKED | Adjustable 9-15 V source needed |
| SYS-ELEC-003 | TC-PATH-001 | BLOCKED | 1 A load and temperature observation needed |
| SYS-ELEC-004 | TC-PATH-001 | BLOCKED | Hardware not built |
| SYS-ELEC-005 | TC-START-001 | NOT RUN | Hardware/firmware not built |
| SYS-ELEC-006 | Design review | NOT RUN | Fast protection topology TBD |
| SYS-ELEC-007 | Design review + controlled fault test | BLOCKED | Current-limited lab setup required |
| SYS-MEAS-001 | TC-MEAS-001 | BLOCKED | Adjustable source needed |
| SYS-MEAS-002 | TC-MEAS-002 | BLOCKED | Prototype needed |
| SYS-MEAS-003 | TC-MEAS-003 | BLOCKED | Load bank needed |
| SYS-MEAS-004 | TC-MEAS-001/002 | BLOCKED | Calibrated reference DMM needed |
| SYS-MEAS-005 | TC-MEAS-003 | BLOCKED | Load bank/reference measurement needed |
| SYS-MEAS-006 | Inspection + later firmware test | NOT RUN | Algorithm not designed |
| SYS-MEAS-007 | TC-MEAS-004 | NOT RUN | Prototype needed |
| SYS-PROT-001 | TC-UVP-001 | BLOCKED | Adjustable source needed |
| SYS-PROT-002 | TC-UVP-002 | BLOCKED | Repeatable transient source/timing needed |
| SYS-PROT-003 | TC-OVP-001 | BLOCKED | Adjustable source needed |
| SYS-PROT-004 | TC-OCP-001 | BLOCKED | Controllable load needed |
| SYS-PROT-005 | TC-OCP-002 + TC-TIME-001 | BLOCKED | Controllable load and timing instrument needed |
| SYS-PROT-006 | TC-RESET-001 | NOT RUN | Prototype needed |
| SYS-PROT-007 | TC-RESET-002 | NOT RUN | Prototype needed |
| SYS-PROT-008 | TC-DIAG-001 | NOT RUN | UI/firmware not built |
| SYS-PROT-009 | Inspection | PLANNED | Enforced by design review |
| SYS-UI-001 | TC-UI-001 | NOT RUN | UI not built |
| SYS-UI-002 | TC-UI-002 | NOT RUN | UI not built |
| SYS-UI-003 | TC-DIAG-001 | NOT RUN | UI not built |
| SYS-UI-004 | TC-RESET-003 | NOT RUN | Input design not built |
| SYS-DIAG-001 | TC-DIAG-002 | NOT RUN | UART format TBD |
| SYS-SAFE-001 | Pre-test checklist | PLANNED | Suitable current-limited source must be obtained |
| SYS-SAFE-002 | Procedure audit | PLANNED | Explicitly prohibited in safety procedure |
| SYS-SAFE-003 | Design review | NOT RUN | Components TBD |
| SYS-SAFE-004 | Pre-test checklist | PLANNED | Test setup dependent |
| SYS-VER-001 | This matrix | PLANNED | Update when requirements change |
| SYS-VER-002 | Result template review | PLANNED | No tests executed |
| SYS-VER-003 | TC-TIME-001 | BLOCKED | Oscilloscope/logic analyzer required |

## Coverage summary

- Static functional behavior is testable with a DMM, appropriate resistive loads and a safely adjustable DC level.
- Dynamic threshold timing is not fully verifiable using only a fixed adapter and multimeter.
- Hard-short behavior must remain blocked until the hardware protection design is reviewed and a current-limited lab source is available.
