# Automatic Number Plate Recognition at the Edge

![ANPR demo](assets/demo.gif)

An edge ANPR engine for access control and parking — detection, OCR, tracking and best-frame selection — rolled out from an 8-camera pilot to 74 cameras.

> This page is the project's showcase. The production source code is proprietary to Anamis Electronic Vira.

**Anamis Electronic Vira (GambroTech), Bandar Abbas** · Computer Vision Engineer · 10/2022 — 02/2025

The company's edge ANPR engine for access control and parking. Frames arrive over RTSP or from Basler industrial cameras; YOLOv4-family detection, OCR post-processing, multi-object tracking and best-frame selection run on the edge device; results go to MySQL and to the server over REST APIs (HTTPS/TLS). Deployed on NVIDIA Jetson Nano and low-power industrial edge PCs.

## Field results — from an 8-camera pilot to 74 cameras

The deployment grew from an 8-camera pilot to 74 cameras at customer parking gates and industrial sites. Field test (09/2024): 1,200 vehicles at barrier gates on GPU-based hardware, manually verified and spread over all 24 hours of the day. No vehicle was missed and 99.4% of plates were read exactly right; each of the 7 errors was a single wrong character on a damaged or dirty plate. A repeat test months apart under similar conditions confirmed the result.

| Result | Value | Context |
|---|---|---|
| Plates read exactly right | **99.4%** | field test · 1,200 vehicles over 24 h |
| Vehicles missed | **0** | same test · confirmed in a repeat test |
| Cameras deployed | **74** | grew from an 8-camera pilot |
| End-to-end throughput | **~12 FPS** | frame ingestion → database · CPU-only edge PC |
| Jetson Nano inference | **~25 FPS** | YOLOv4-tiny, TensorRT FP16 · up to 4 cameras |
| Vehicle capture, Intel Atom | **<30% → ~70%** | same compute budget |

## Data and model

- Trained and evaluated YOLOv4-family detection models (YOLOv4-tiny for edge deployment) on a 7,000+ image field dataset, balancing accuracy and speed for low-power hardware.
- I selected and curated the dataset (vehicles, motorcycles, plates; varied weather, lighting and angles) and defined its structure; annotation was done by a fellow trainee.

## Real-time C++ pipeline

- Migrated performance-critical parts from Python to multithreaded C++: RTSP frame ingestion, YOLO inference, OCR post-processing, MySQL storage and camera reconnection handling.
- Evaluated OpenCV, GStreamer and libVLC as streaming backends to cope with unstable camera links.
- Added motion-gated processing (skipping static frames), direction-of-travel detection, multi-object tracking and best-frame selection per vehicle to improve OCR reliability.

## Running on weak edge hardware

- Up to ~12 FPS end to end, from frame ingestion to database, on the standard company edge PCs (CPU-only).
- On NVIDIA Jetson Nano, optimised YOLOv4-tiny with TensorRT (FP16) to ~25 FPS inference, serving up to 4 gate cameras per device with motion gating. Added a CUDA-accelerated OpenCV DNN mode for multi-camera sites.
- On the weakest hardware (Intel Atom E3845, CPU only, pipeline capped at ~5 FPS vs. a 15 FPS camera), at a gate without a barrier where vehicles passed faster, a motion-gated centre-ROI buffer feeding a dedicated inference thread raised vehicle capture from under 30% to ~70% within the same budget; peak traffic could still overflow the buffer.
- Reduced RAM and CPU usage to run on Jetson Nano and other low-power industrial edge PCs.

## Cameras and integration

- Integrated Basler industrial cameras via the Pylon SDK in C++: frame acquisition, exposure control and synchronisation with the LED flash.
- Implemented REST APIs over HTTPS/TLS for camera-to-server data transfer and for the web UI to control the engine and fetch results.
- Wrote a native C++ DLL with the plate-recognition logic, exposed via P/Invoke and used in production by the UI team's C# application.

## Tech stack

C++ · Python · OpenCV · YOLOv4 / YOLOv4-tiny · CUDA · TensorRT (FP16) · NVIDIA Jetson Nano · Embedded Linux · Multithreading · RTSP · GStreamer · libVLC · Basler Pylon SDK · MySQL · REST APIs (HTTPS/TLS) · Multi-object tracking · C++ DLL / P/Invoke

*Published with the permission of Anamis Electronic Vira.*

## License

[MIT](LICENSE) — applies to the documentation in this repository.
