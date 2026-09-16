# Basic bench setup

## Purpose

Define the minimum repeatable setup for static measurement and nominal-load testing. This is not a hard-short setup.

## Connection

```text
adjustable/current-limited DC source
              |
              v
          station DC IN
          station DC OUT
              |
              v
      rated resistor/load bank
```

Connect the reference DMM at the exact terminals relevant to the test. Do not infer terminal voltage from an adapter label or an unloaded setting.

## Load selection examples

For a primarily resistive load:

`R = V / I` and `P = V x I`.

| Target at 12 V | Approximate load | Dissipation |
| ---: | ---: | ---: |
| 0.25 A | 48 ohm | 3 W |
| 0.50 A | 24 ohm | 6 W |
| 0.90 A | 13.3 ohm | 10.8 W |
| 1.00 A | 12 ohm | 12 W |
| 1.20 A | 10 ohm | 14.4 W |

Select a practical resistor power rating comfortably above calculated dissipation, mount it safely and expect it to become hot. Actual current must be measured because resistor tolerance and heating change the value.

## Limitation of a fixed 12 V adapter

A fixed adapter can support a nominal demonstration but cannot produce repeatable 10.5 V UVP and 14.2 V OVP thresholds. Do not improvise over-voltage using unregulated or unknown supplies. Arrange an adjustable regulated source for those cases.
