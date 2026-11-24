# MCU–HW_OCP interface review — 2026-11-23

## Scope and saved change

The two logic signals originally connected Blue Pill and U7 directly despite their separate 3.3 V supplies. This batch inserts a buffered interface. MCU pins, active-HIGH meanings, the NAND latch equations and the direct hardware shutdown path remain unchanged.

| Signal | New path | Supply and default |
| --- | --- | --- |
| Clear request | A1.39 → R49 → Q8 gate; Q8 drives U8 /OE; U8 A is tied HIGH; U8 Y → U7.1 | U8 uses V_CTRL_3V3. R48 pulls the MCU request LOW, R50 pulls /OE HIGH, and R21 pulls the disabled output LOW. |
| Fault status | U7.11 → U9 A; U9 Y → A1.40 | U9 uses V_BLUEPILL_3V3 and /OE is grounded. R52 terminates its input and R51 its output. Status is meaningful only with valid HW_OCP power. |

Added U8/U9 = TI SN74LVC1G125DBVR, Q8 = UMW AO3400A, R48/R49/R50/R51/R52 and C15/C16. R21 changed from 10 kΩ to 47 kΩ. No bulk property or footprint assignment was performed.

The clear buffer is normally disabled, rather than continuously enabled during startup. A deliberate MCU HIGH request enables its local HIGH output. When the MCU request returns LOW or becomes high impedance, the request pull-down turns Q8 off and the latch-side clear returns LOW. This does not create a power-on latch reset.

## Evidence and limits

- Exported XML netlist: 116 components. Verified every added pin and the two intentional signal splits. All other existing pin-to-pin net memberships were unchanged.
- KiCad ERC: 0 errors and 0 warnings with the existing project configuration. Four checks remain excluded: single-instance global labels, four-way junctions, SPICE model issues, footprint-filter mismatch.
- Enumerated 16 steady Boolean cases across prior fault state, new overcurrent, clear request and MCU power presence. An overcurrent sets/holds shutdown despite clear; removing the MCU request does not clear a retained fault.
- PDF render inspected; new symbols and labels fit in the lower part of the root sheet.
- These checks are not a transient simulation or physical hardware validation. The Boolean model assumes valid latch power; it makes no claim about analog rail ramps, partially powered GPIO, propagation timing or threshold tolerances.

## Electrical basis

SN74LVC1G125 accepts input voltage independently of its supply within its specified limits and provides partial-power-down protection at VCC = 0 V. Its specified Ioff is at most ±10 µA. This is not a guarantee throughout an arbitrary brownout. U8 /OE is pulled toward its own supply, following the manufacturer's power-up/down recommendation when Q8 is off.

With a 47 kΩ pull-down, a conservative 11 µA combined output/input leakage gives approximately 0.54 V allowing +5% resistor tolerance. At 3.6 V and −5% resistance, pull-down load plus 1 µA receiver leakage is about 82 µA, within the buffer's 100 µA VOH test condition. These are static estimates. U7 now drives U9 input plus R52 (less than approximately 86 µA at 3.6 V); final U7 vendor/grade and loaded logic margin must still be qualified.

No positive clamp-diode connection between the two supply rails is intentionally introduced by this interface. Finite leakage remains; this is not galvanic isolation or proof that all other board backfeed paths have been eliminated.

## Procurement and primary sources

- [IC Đây Rồi — SN74LVC1G125DBVR](https://icdayroi.com/sn74lvc1g125dbvr): product listing located. This establishes a local sourcing lead, not a reservation or guaranteed stock.
- [TI SN74LVC1G125 datasheet](https://www.ti.com/lit/ds/symlink/sn74lvc1g125.pdf): DBV pinout /OE=1, A=2, GND=3, Y=4, VCC=5; partial-power-down, leakage and logic specifications. Do not substitute a generic HC125.
- [IC Đây Rồi — UMW AO3400A](https://icdayroi.com/ao3400a-mosfet-sot23-3-n-1ch-5a-30v-umw).
- [UMW AO3400A datasheet](https://www.umw-ic.com/static/pdf/84856d88d376898b76b15e1974fc7647.pdf): G=1, S=2, D=3; operation specified at VGS=2.5 V. Q8 only sinks the /OE pull-up current.

## Outstanding before functional sign-off

1. Power-valid interlock and deterministic latch initialization are still unresolved. The buffers do not implement either function.
2. HW_OCP fault status LOW must not be interpreted as proof of valid HW_OCP power. An invalid/missing latch supply invalidates that status.
3. Qualify slow ramps, brownouts, clear-line edges and supply recovery without automatic load turn-on. U9 /OE is continuously LOW; no valid-status guarantee is made while its own supply is ramping.
4. Retained fault must survive an MCU-only reset and loss of 5 V while the latch supply remains valid. Do not automatically clear the latch during every startup window.
5. Remaining checks in the local schematic checkpoint still apply: power-switch timing, clamp energy, power budget, measurement accuracy, fuse coordination and exact purchase availability.

Reproduction artifacts for this batch are local under `.tmp/interface-buffer/`: before/staged schematic, XML netlist, ERC, rendered PDF/PNG, build and verification scripts. No firmware implementation or bench test was performed.
