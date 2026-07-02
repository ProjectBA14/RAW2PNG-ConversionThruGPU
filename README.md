# RAW2PNG-ConversionThruGPU

GPU-accelerated image conversion pipeline for converting **DICOM**, **TIFF**, and **Camera RAW** images into PNG using CUDA-based image processing and a high-performance **zlib-ng** compression backend.

The project overlaps image loading, GPU preprocessing, compression, and PNG writing through a concurrent pipeline architecture designed for high-throughput image conversion.

---

# Features

* CUDA-accelerated image preprocessing
* Supports DICOM, TIFF, and Camera RAW formats
* JPEG, JPEG-LS, JPEG2000 and RLE DICOM decoding
* OpenJPEG integration
* zlib-ng accelerated DEFLATE compression
* Manual PNG generation
* Strip-based GPU processing
* Multi-threaded producer-consumer pipeline
* Performance benchmarking utilities
* Supports both legacy (GT710) and modern NVIDIA GPUs

---

# Project Structure

```text
.
├── benchmarks/         Benchmark reports and graphs
├── include/            Header files
├── src/                Source files
├── data/               Sample data
├── CMakeLists.txt
└── README.md
```

---

# Requirements

## Operating System

* Windows 10/11
* Linux (tested with recent Ubuntu releases)

---

## Compiler

* C++17 compatible compiler
* MSVC 2022
* GCC 11+
* Clang 14+

---

## CMake

Minimum version:

```
3.18
```

For modern GPU builds (automatic architecture detection):

```
3.24 or newer
```

---

# CUDA Requirements

## Legacy GPU (GT710)

* CUDA Toolkit **11.8**
* Compute Capability **3.5**

Build using:

```bash
cmake -B build -DLEGACY_GPU=ON
```

---

## Modern GPUs (RTX Series)

* CUDA Toolkit 12.x or newer
* Recommended CUDA 13.x
* CMake 3.24+

Build using:

```bash
cmake -B build
```

---

# Required Libraries

The following libraries must be installed before building.

| Library                           | Purpose                 |
| --------------------------------- | ----------------------- |
| CUDA Toolkit                      | GPU acceleration        |
| zlib-ng (zlib compatibility mode) | PNG compression         |
| libpng                            | PNG writing             |
| libtiff                           | TIFF image loading      |
| LibRaw                            | Camera RAW decoding     |
| DCMTK                             | DICOM parsing           |
| OpenJPEG                          | JPEG2000 DICOM decoding |

---

# Installing Dependencies

## Using vcpkg (Recommended)

Install the required libraries:

```bash
vcpkg install libpng
vcpkg install libtiff
vcpkg install libraw
vcpkg install dcmtk
vcpkg install openjpeg
vcpkg install zlib-ng[core,zlib-compat]
```

If using CMake with vcpkg:

```bash
cmake -B build ^
    -DCMAKE_TOOLCHAIN_FILE=<vcpkg-root>/scripts/buildsystems/vcpkg.cmake
```

Replace `<vcpkg-root>` with your local vcpkg installation path.

---

# Building

Clone the repository:

```bash
git clone https://github.com/ProjectBA14/RAW2PNG-ConversionThruGPU.git

cd RAW2PNG-ConversionThruGPU
```

## Modern GPU Build

```bash
cmake -B build

cmake --build build --config Release
```

---

## GT710 / Legacy Build

```bash
cmake -B build -DLEGACY_GPU=ON

cmake --build build --config Release
```

---

# Running

After compilation, execute:

```bash
gpu_png_encoder input_image output.png
```

Example:

```bash
gpu_png_encoder sample.dcm output.png
```

---

# Command Line Options

| Option             | Description                            |
| ------------------ | -------------------------------------- |
| `--strip-height N` | Number of rows processed per GPU strip |
| `--threads N`      | Number of compression worker threads   |
| `--level N`        | PNG compression level (0–9)            |
| `--verbose`        | Print timing and pipeline statistics   |

Example:

```bash
gpu_png_encoder image.dcm output.png --strip-height 128 --threads 4 --level 0 --verbose
```

---

# Supported Input Formats

* DICOM (.dcm)
* TIFF (.tif/.tiff)
* Camera RAW (CR2, NEF, ARW, DNG, etc.)

---

# DICOM Features

Supported Transfer Syntaxes:

* Implicit VR Little Endian
* Explicit VR Little Endian
* JPEG Baseline
* JPEG Lossless
* JPEG-LS
* JPEG2000
* JPEG2000 Lossless
* RLE Lossless

The processing pipeline automatically performs:

* Pixel extraction
* Rescale Slope / Intercept
* Window Width / Level
* Bit-depth normalization
* PNG scanline filtering

---

# Performance

The pipeline records timing information for:

* Image loading
* Host → Device transfer
* CUDA kernel execution
* Device → Host transfer
* Compression
* PNG writing
* Total conversion time

Benchmark reports are available under:

```text
benchmarks/
```

---

# Notes

* **Compression Level 0** is recommended for maximum throughput.
* **zlib-ng** is used in **zlib compatibility mode**, making it a drop-in replacement for zlib while providing significantly better performance.
* Modern GPUs automatically enable the optimized CUDA pipeline.
* Legacy GPUs fall back to a compatible processing pipeline while preserving functionality.

---

# License

This project is intended for academic research and performance evaluation.
