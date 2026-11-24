# Power-sequence investigation — 2026-11-23

Status: reference-bias correction ready for live schematic; backup-domain proposal is experimental and NOT a completed startup/interlock solution. No product-policy approval is inferred from this investigation.

## Reference-bias correction

R9 changes from 1.5 kΩ / 0.5 W to 1 kΩ / 1 W / 1%. Connectivity and the reference-divider ratios do not change.

At the previously proposed 4.06 V falling threshold, the old nominal circuit gives:

```
I_R9 = (4.06 - 2.495) / 1500 = 1.043 mA
I_dividers = 2.495 / 44000 + 2.495 / 27680 = 0.147 mA
I_TL431 = 0.896 mA
```

Thus the old resistor cannot support a 1 mA TL431 design requirement down to that threshold. This finding does not establish an actual undervoltage cutoff; no such cutoff is added by this batch.

Screening the new resistor with Vref = 2.44–2.55 V, R9 ±1%, existing divider resistors ±1%, and an extra 10 µA load allowance gives:

- At 4.00 V: minimum calculated TL431 cathode current approximately 1.274 mA.
- At 24 V: maximum calculated R9 dissipation approximately 0.470 W, below its new 1 W nominal rating. Final footprint and temperature derating remain required.
- Reference-only minimum supply for 1 mA in this envelope: approximately 3.723 V. This is NOT the system undervoltage threshold and does not include the comparator common-mode/startup proof.
- At 15 V nominal, the increased input draw is approximately 4.17 mA / 62.5 mW; it must be included in the complete self-consumption budget.

The stated Vref interval is a conservative screening assumption; final temperature/grade limits and all additional loads must be checked before protection-threshold sign-off.

[Local 1 kΩ / 1 W / 1% listing](https://icdayroi.com/dien-tro-1k-1w-1), [TI TL431 datasheet](https://www.ti.com/lit/ds/symlink/tl431.pdf).

## Why MCU POR alone is insufficient

A Blue Pill VDD brownout can set its POR/PDR flag while the external latch supply remains valid and retains a genuine fault. Automatically clearing based on the MCU reset flag would erase that fault. No live implementation uses that proposed rule.

## Experimental backup-domain approach

STM32F103 provides backup registers that can be maintained from VBAT when its main VDD is absent. Its documented VBAT operating range is 1.8–3.6 V. The project symbol exposes VBAT as module pin A1.1; the current live board connects it to V_BLUEPILL_3V3.

Candidate: feed A1.1 from V_CTRL_3V3 instead, so a marker can survive MCU-only power loss while the latch retains valid power. No battery and no Flash writes are required. This does not power the CPU from VBAT. The backup block can also retain from VDD; therefore a retained marker is a conservative reason NOT to automatically reinitialize, not a precise history of the latch rail.

The separate experimental schematic changes only A1.1's supply connection (plus the R9 correction). It is NOT published into the live schematic. Before adoption, verify the purchased Blue Pill has an independent VBAT header, no hard connection to its 3.3 V rail, a supported STM32 variant, suitable local decoupling and correct power switchover behavior.

Proposed protocol constraints, not approved production firmware:

1. All starts keep MCU_ENABLE and CLEAR LOW. A hardware interlock must independently hold the output OFF.
2. A known initialized marker forbids automatic latch initialization even when MCU POR/PDR is set.
3. Partial writes, unknown data and ambiguous retention conditions keep OFF and require recovery; they are not interpreted as a fresh boot.
4. Consume any one-time initialization eligibility by writing and verifying the marker BEFORE issuing an initialization pulse. A reset between writes or before finishing must not create another automatic-clear attempt.
5. A zero backup value is only a possible fresh-domain indication under explicit assumptions. Firmware must not reset the backup domain or enable tamper erasure during normal initialization. A lost/corrupt marker must never casually authorize clearing a known real fault.
6. This marker alone does NOT distinguish an initially unknown latch from a real OCP event occurring during startup. Cold initialization still needs a hardware sequence that prevents erasing such an event, and must be reconciled with the existing rule to preserve a reported HW_OCP fault.

[STM32F103 datasheet, sections 2.3.9 and 2.3.14](https://www.st.com/resource/en/datasheet/stm32f103c8.pdf), [ST AN2629](https://www.st.com/resource/en/application_note/an2629-stm32f101xx-stm32f102xx-and-stm32f103xx-lowpower-modes-stmicroelectronics.pdf), [reference Blue Pill schematic](https://stm32-base.org/assets/pdf/boards/original-schematic-STM32F103C8T6-Blue_Pill.pdf).

## Checks performed and remaining gates

- R9 revision: XML netlist confirms all nets and component count unchanged; only R9 value changed. ERC 0 errors / 0 warnings with the same four existing exclusions. PDF crop inspected.
- VBAT experiment: XML netlist confirms only A1.1 moves between the two supply nets. This is a connectivity check, not qualification of the module.
- Host-side eligibility model: 20 input combinations, 8 reset-after-marker cases, and 32 single-bit marker-corruption cases checked. The model reproduces the POR-only counterexample. It does not simulate analog supplies or constitute MCU firmware.
- Local artifacts: `.tmp/power-sequence-candidate/` contains the baseline, R9-only schematic, separate VBAT experiment, netlists, ERC/PDF, `check_sequence.py`, and its JSON result.

Still required before applying the startup topology:

1. Default-OFF hardware interlock even when the voltage comparator or its reference is not valid. Check retained charge and USB/debug power; do not assume all rails collapse together.
2. Direct qualification of 5 V sensing power, latch power, and comparator/reference supply. 3.3 V presence alone is not a proof of instantaneous 5 V validity.
3. Retained OVP/power-failure event so a short input disconnect cannot automatically restore power to the load.
4. Cold-start initialization that cannot erase a genuine event, without broadening the approved recovery policy. The backup marker is only one part of this design.
5. Exact LM339 variant, bias/offset/hysteresis calculation and transient simulation or bench verification.

## Sourcing update

ThegioiIC's actual [voltage-supervisor category](https://www.thegioiic.com/san-pham/ic-giam-sat-dien-ap) was inspected in the browser: all six displayed entries were marked out of stock at the time of this check (ADM1069ASTZ, MAX811LEUS+T, DS1231-20+, M51957BFP, M51953BFP and MCP130T-315I/TT). This does not prove no domestic supervisor exists. No unavailable supervisor has been selected for the schematic.
