# Xiaomi 11 Battery Discharge Dataset

Raw Xiaomi 11 measurements used in **A Low-Order Continuous-Time Electrothermal Model for Smartphone Time-to-Empty Prediction and Uncertainty Quantification**, manuscript **V4.4**.

The dataset contains **42 measurement records**, **567 CSV files**, and **3,605,489 data rows** (963,688,544 raw-data bytes). Each workload has seven records at room temperature and seven at low temperature.

## Directory structure

```text
.
├── README.md
├── room_temperature/           # Ambient temperature: 25°C; 21 records
│   ├── local_video_playback_1/
│   ├── online_video_playback_1/
│   ├── online_gaming_1/
│   └── ...
└── low_temperature/            # Ambient temperature: 9°C; 21 records
    ├── local_video_playback_1/
    ├── online_video_playback_1/
    ├── online_gaming_1/
    └── ...
```

Each record directory contains numbered CSV parts. Directory names use the workload names from the manuscript followed by a record number from 1 to 7 within each temperature condition.

| Manuscript workload | Directory prefix | Room-temperature records | Low-temperature records |
|---|---|---|---|
| Local Video Playback | `local_video_playback_` | 7 | 7 |
| Online Video Playback | `online_video_playback_` | 7 | 7 |
| Online Gaming | `online_gaming_` | 7 | 7 |

Local Video Playback loops a long video in the built-in gallery application. Online Video Playback uses continuous playback in Bilibili. Online Gaming uses Arknights.

## Download and read

Use **Code → Download ZIP**, or clone this repository with Git.

CSV files retain their original filenames, Chinese column headers, UTF-8 BOM encoding, timestamps, values, missing cells, and line endings. No interpolation, gap filling, resampling, or model predictions have been written into them.

For one record, read parts in numeric `part-001`, `part-002`, ... order and combine their data rows. Sort by `样本序号` (sample sequence). Use `设备运行时间(秒)` (device uptime) minus its first value as elapsed time; host UTC timestamps may contain repeated or backward steps. Python readers can use `encoding="utf-8-sig"`.

| Original column | Meaning / unit |
|---|---|
| 样本序号 | Sample sequence within the record |
| 时间 | Host ISO 8601 timestamp; `Z` denotes UTC |
| 设备运行时间(秒) | Device uptime, seconds |
| 实际间隔(秒) | Recorded sample interval, seconds |
| CPU使用率(%) | CPU utilization, percent |
| 内存使用率(%) | Memory occupancy, percent |
| WiFi下载速率(MiB每秒), WiFi上传速率(MiB每秒) | Wi-Fi download/upload rates, MiB/s |
| 蜂窝下载速率(MiB每秒), 蜂窝上传速率(MiB每秒) | Cellular download/upload rates, MiB/s |
| SOC(%) | System-reported battery state of charge, percent |
| 剩余电荷(mAh) | System-reported remaining charge, mAh |
| 端电压(V) | System-reported terminal voltage, V |
| 电池温度(摄氏度) | System-reported battery temperature, °C |
| 屏幕状态 | Screen state; `亮屏` means screen on |
| 实际归一化亮度 | Normalized brightness, dimensionless |

Additional columns retain their original labels and units. Missing cells mean unavailable readings, not measured zeros. Ambient-temperature directory labels are distinct from the recorded battery temperature. GPU memory readings do not represent GPU utilization.

## Measurement notes

Measurements were collected through non-root wireless ADB monitoring. The main sampling rate is approximately 4 Hz; some device states and Perfetto-window fields update more slowly. Monitoring overhead is included in the observed workload. Some records contain sampling gaps or screen-off observations, which are preserved in the raw files.

Logged endpoints and physical shutdown evidence should be distinguished. In particular, `low_temperature/local_video_playback_7` runs from 99% SOC to its final logged 1% SOC sample for 31,099.38 seconds; that final sample is its comparison endpoint in the manuscript. A filename containing `disconnect` identifies a connection event.

This repository provides raw measurements for reviewer and reader access. Analysis code, derived results, and third-party datasets are outside this data release.
