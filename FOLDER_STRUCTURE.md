# Folder Structure

A map of the repository so anyone can find their way around quickly. The single most
important split is **`firmware/` (real-time MCU side)** versus **`mpu/` (Linux/Python
compute side)**. That boundary mirrors the Arduino UNO Q's dual-brain architecture: the
Arduino Bridge is the dividing line between them.

```
MachineSense/
├── README.md                  Project overview, problem, what it does, how to run
├── LICENSE                    MIT license (Happy Electrons)
├── .gitignore                 Files Git should not track (build artifacts, caches, secrets)
├── FOLDER_STRUCTURE.md        This file
│
├── docs/                      All documentation and design artifacts
│   ├── architecture/          System block diagram (.drawio) + exported architecture.png
│   ├── hardware/              Hardware docs
│   │   └── schematics/        Circuit schematics (wiring diagrams, board pinouts)
│   ├── report/                Working drafts of the project report (PDF deliverable)
│   └── images/                Rig photos, dashboard screenshots, figures for the report
│
├── firmware/                  MCU side: STM32U585, Arduino C++ (hard real-time)
│   └── machinesense_daq/      Sketch: fast sampling, framing, Bridge streaming, alert output
│
├── mpu/                       MPU side: Debian Linux, Python (heavy compute)
│   ├── daq/                   Receive sensor frames over the Bridge, log to disk
│   ├── features/              Windowing, FFT, time-domain + statistical feature extraction
│   ├── inference/             Load the quantized model, classify health, send alert back
│   └── dashboard/             Web dashboard and alert UX
│
├── ml/                        Offline model-development pipeline (runs on a dev machine)
│   └── notebooks/             EDA: per-class plots, correlation heatmap, feature importance
│
├── data/                      Datasets
│   ├── raw/                   Raw sensor captures, straight from the DAQ pipeline
│   └── processed/             Windowed / feature-extracted data ready for training
│
└── models/                    Trained and quantized models (e.g. .tflite) + model cards
```

## How the pieces connect

```
Motor rig
   │  vibration / temperature / voltage / current
   ▼
Sensors ──► firmware/  (MCU: sample fast, frame, stream)
                │  Arduino Bridge (RPC, bidirectional)
                ▼
            mpu/  (windowing ► features ► inference ► health score)
                │                                          │
                ▼                                          ▼
          mpu/dashboard/                            alert back to MCU
```

The offline side (`ml/`) is separate from the runtime: it consumes `data/`, produces a
model in `models/`, and that model is what `mpu/inference/` loads on the board.