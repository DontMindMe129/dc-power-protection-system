# Powered-test safety procedure

## Mandatory rules

- Work only with isolated low-voltage DC within the approved 9-15 V envelope.
- Do not connect the project directly to mains voltage.
- Verify source polarity and actual voltage with a DMM before connection.
- Use a source with known current limiting, or add a reviewed fuse/series limit appropriate to the stage.
- Keep an accessible input power disconnect.
- De-energize the setup before changing wiring or load values.
- Treat power resistors as burn hazards; place them on a nonflammable surface with clearance.
- Do not rely on a solderless breadboard for the final 1 A power path without explicit evaluation.
- Do not short `DC OUT` using an uncontrolled fixed adapter.
- Stop immediately for smoke, odor, arcing, unstable current, repeated MCU reset or unexpected heating.

## Pre-test checklist

- [ ] Test-case revision identified.
- [ ] Hardware and firmware revisions recorded.
- [ ] Wiring checked against a diagram.
- [ ] Source voltage verified before connection.
- [ ] Current limit or fuse verified.
- [ ] Load value and power rating calculated.
- [ ] DMM leads are in the correct jacks and mode.
- [ ] Emergency power disconnect is reachable.
- [ ] Expected readings and stop thresholds are written down.
- [ ] No unauthorized short-circuit step is present.

## After test

Remove input power, allow power resistors to cool, save raw measurements and record any anomaly before altering the setup.
