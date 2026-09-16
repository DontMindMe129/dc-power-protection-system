# DC Power Monitoring and Protection System

> Status: feasibility and requirements phase. No hardware or firmware implementation has been approved yet.

This repository contains the complete engineering record for a university Embedded System Design project. The proposed system is installed between an external DC source and an external load. It measures the electrical state of the source and load, warns the user about abnormal conditions, and disconnects the load when a configured protection condition is confirmed.

```text
External DC source
        |
        v
DC IN -> input protection -> sensing -> load switch -> DC OUT
                               |             ^
                               v             |
                         STM32 controller ---+
                               |
                         OLED / buttons /
                         buzzer / UART
        |
        v
External load
```

## Current baseline

The values below are the baseline for feasibility analysis. Values marked **Provisional** must be confirmed after component selection and prototype measurement.

| Item | Current decision |
| --- | --- |
| Nominal source | 12 V DC |
| Supported input for measurement/protection evaluation | 9-15 V DC |
| Maximum continuous load current | 1.0 A |
| Primary measurements | `Vin`, `Vload`, `Iload` |
| V1 protection scope | UVP, OVP, OCP |
| Trip behavior | Latched trip; manual reset after the fault clears |
| Short-circuit response | Fast hardware protection; firmware records/indicates the event when possible |
| Over-temperature protection | Out of scope for V1 |
| Candidate controller | STM32F103C8T6 |
| Candidate UI | SSD1306 OLED, three buttons, LED/buzzer, UART debug |
| Available test resources | Basic tools; limited adjustable/current-limited lab equipment |

## Repository map

| Path | Purpose | Current detail level |
| --- | --- | --- |
| `docs/requirements/` | Product and system requirements, use cases, traceability, feasibility | Detailed |
| `docs/specifications/` | Design, hardware, software and test specifications | Test specification detailed; others placeholders |
| `docs/architecture/` | System context, partitioning and interfaces | Placeholder |
| `firmware/` | MCU firmware project | Placeholder |
| `hardware/` | Schematic, PCB, simulation, BOM and manufacturing data | Placeholder |
| `verification/` | Test plan, procedures, cases and results | Detailed plan and cases; no results yet |
| `deliverables/` | Report, slides and demo material | Placeholder |
| `references/` | Datasheets and source index | Placeholder |
| `project/` | Milestones, ownership and contribution workflow | Initial skeleton |

## Start here

1. Read [`docs/requirements/product-requirements.md`](docs/requirements/product-requirements.md).
2. Review the measurable requirements in [`docs/requirements/system-requirements.md`](docs/requirements/system-requirements.md).
3. Check test coverage in [`docs/requirements/requirements-traceability.md`](docs/requirements/requirements-traceability.md).
4. Read the feasibility conclusion in [`docs/requirements/feasibility-assessment.md`](docs/requirements/feasibility-assessment.md).
5. Before any powered test, follow [`verification/procedures/safety.md`](verification/procedures/safety.md).

## Decision rule for proceeding

The project may proceed to architecture and component selection only if:

- every safety-critical requirement has a credible verification method;
- static voltage/current measurement can be tested with available equipment;
- the team can obtain temporary access to a current-limited adjustable source and a timing instrument for final protection-response verification;
- the load switch, current-sense circuit and PCB power path can safely carry 1 A continuously;
- direct short-circuit testing is not attempted before fast hardware protection has been independently reviewed and current-limited.

## License

No open-source license has been selected. Do not assume redistribution rights until the team chooses a license.
