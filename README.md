# RAW2PNG-ConversionThruGPU

GPU-accelerated image conversion pipeline for converting **DICOM**, **TIFF**, and **Camera RAW** images into PNG using CUDA-based image processing and multi-threaded DEFLATE compression.

The project is designed to maximize throughput by overlapping image loading, GPU filtering, CPU compression, and PNG writing through a concurrent pipeline architecture.

---

## Features

- CUDA-accelerated image preprocessing
- Supports DICOM, TIFF, and Camera RAW formats
- Multi-threaded DEFLATE compression
- Strip-based image processing for low memory usage
- Concurrent producer-consumer pipeline
- JPEG, JPEG-LS, JPEG2000, and RLE DICOM support
- OpenJPEG integration for JPEG2000 decoding
- Manual PNG generation with custom IDAT stream handling

---

## Architecture

```text
Input Image
      │
      ▼
┌─────────────┐
│ Loader      │
└─────────────┘
      │
      ▼
 Queue A
      │
      ▼
┌─────────────┐
│ GPU Filter  │
│ CUDA        │
└─────────────┘
      │
      ▼
 Queue B
      │
      ▼
┌─────────────┐
│ DEFLATE     │
│ ThreadPool  │
└─────────────┘
      │
      ▼
 Queue C
      │
      ▼
┌─────────────┐
│ PNG Writer  │
└─────────────┘
      │
      ▼
    PNG
```

---

## DICOM Support

Supported transfer syntaxes:

- Implicit VR Little Endian
- Explicit VR Little Endian
- JPEG Baseline
- JPEG Lossless
- JPEG-LS Lossless
- JPEG-LS Near Lossless
- JPEG 2000 Lossless
- JPEG 2000
- RLE Lossless

The pipeline performs normalization, rescaling, windowing, and PNG preparation before compression.

---

## GPU Processing

CUDA kernels accelerate:

- Pixel preprocessing
- DICOM transformations
- PNG scanline filtering

Performance metrics can be collected for:

- Host → Device transfer
- Kernel execution
- Device → Host transfer

---

## Parallel Compression

Each image strip is divided into chunks and compressed in parallel using a persistent thread pool.

Default configuration:

```cpp
Threads = 6
Compression Level = 3
```

---

## PNG Generation

```text
Image Data
    │
    ▼
PNG Filters
    │
    ▼
DEFLATE Compression
    │
    ▼
IDAT Chunks
    │
    ▼
PNG File
```

The encoder manually constructs PNG chunks for greater performance control.

---

## Dependencies

- CUDA Toolkit 11.8+
- DCMTK
- OpenJPEG
- libpng
- zlib
- libraw
- libtiff
- CMake 3.18+
- C++17

---

## Build

```bash
git clone https://github.com/ProjectBA14/RAW2PNG-ConversionThruGPU.git
cd RAW2PNG-ConversionThruGPU

cmake -B build
cmake --build build --config Release
```

---

## Usage

```bash
gpu_png_encoder input.dcm output.png
```

### Options

```bash
gpu_png_encoder input.dcm output.png --strip-height 128
gpu_png_encoder input.dcm output.png --threads 8
gpu_png_encoder input.dcm output.png --level 3
gpu_png_encoder input.dcm output.png --verbose
```

| Option | Description |
|----------|----------|
| --strip-height N | Rows processed per GPU strip |
| --threads N | Number of compression worker threads |
| --level N | Compression level |
| --verbose | Print performance metrics |

---

## Project Structure

```text
include/
src/
CMakeLists.txt
README.md
```

---

## Future Improvements

- zlib-ng integration
- Compression level 0 benchmarking
- Pinned-memory GPU transfers
- Async CUDA streams
- Multi-GPU support
- Batch DICOM conversion
- GPU-based PNG filtering

---

## License

This project is intended for research, learning, and high-performance image processing experimentation.
