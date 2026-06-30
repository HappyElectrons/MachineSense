# MachineSense

> Edge-AI predictive maintenance for electric motors: an always-on, on-device fault-detection node built on the Arduino UNO Q.

Built for the **Arduino Physical AI Challenge India 2026**, Industrial & Sustainability AI track.

## The problem
Electric motors are the workhorse of almost all machinery (robotics, EVs, HVAC, pumps,
and factory equipment) and they fail through predictable modes: rotor imbalance,
mechanical looseness, overheating, and electrical faults like overload or undervoltage.
Left undetected these cause sudden breakdowns and unplanned downtime, yet most equipment
is still run-to-failure or serviced on rigid fixed schedules that waste component life
while still missing early faults.

## What it does
MachineSense is a low-cost, retrofittable node that senses **vibration, temperature,
voltage, and current** on a running motor. Real-time sampling happens on the board's MCU;
the data is streamed over the Arduino Bridge to the Linux MPU, where FFT-based feature
extraction and an on-device ML model classify the machine's health state and flag
developing faults **before failure**, always-on and cloud-free. It is demonstrated as a
proof of concept on a low-voltage DC/BLDC motor bench rig with controlled fault induction;
because the sensed signals are generic to all electric motors, the method generalizes
across motor-driven equipment.

## Hardware
- **Arduino UNO Q**, dual-brain: STM32U585 (MCU, real-time) + Qualcomm Dragonwing QRB2210 (MPU, Debian Linux)
- Sensors: accelerometer (vibration), temperature, voltage, current
- Demonstrated on a low-voltage DC/BLDC motor bench rig (proof of concept)

Full bill of materials: [`docs/hardware/BOM`]

## Architecture
![Architecture](docs/architecture/architecture.png)

Sensors to MCU (real-time sampling + framing) to Arduino Bridge to MPU
(windowing, FFT + features, ML inference, health score) to dashboard + alerts.

## Health states
`Healthy` · `Imbalance` · `Looseness` · `Overheating` · `Electrical/Overload`

## Repository layout
- `firmware/` : MCU sketch (sampling, framing, Bridge streaming, alert output)
- `mpu/` : Python for DAQ, feature extraction, inference, dashboard
- `ml/` : offline training pipeline + EDA notebook
- `data/`, `models/` : datasets and trained models (see each README)
- `docs/` : BOM, schematics, architecture, report, submission checklist

## Status
Work in progress. See the project roadmap / issues.

## License
MIT, see [LICENSE](LICENSE).
