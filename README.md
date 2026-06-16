# RAW2PNG-ConversionThruGPU

**GPU-accelerated DICOM, TIFF, and Camera RAW to PNG conversion pipeline built with CUDA and zlib-ng.**

This project converts high-resolution medical and photographic images into PNG using a hybrid CPU/GPU architecture that combines:

- CUDA-based image processing
- Strip-based streaming
- Parallel DEFLATE compression
- Concurrent producer-consumer pipelines
- PNG generation with custom IDAT stream construction

The design prioritizes **throughput**, **low memory usage**, and **scalability for large images**.

---

## Supported Formats

### Input

#### DICOM

- `.dcm`
- `.dicom`
- `.dic`
- `.ima`
- Files containing DICOM magic (`DICM`) at byte offset 128

#### TIFF

- `.tif`
- `.tiff`

#### Camera RAW

- `.cr2`
- `.nef`
- `.dng`
- `.arw`
- `.raf`
- `.orf`
- `.rw2`
- `.raw`

### Output

- PNG (`.png`)

---

## Key Features

### CUDA Image Processing

GPU-accelerated operations include:

- Pixel normalization
- Window/Level transformations
- Rescale slope/intercept application
- Bit-depth conversion
- PNG scanline preparation

### Advanced DICOM Support

Supported transfer syntaxes:

- Implicit VR Little Endian
- Explicit VR Little Endian
- JPEG Baseline
- JPEG Lossless
- JPEG-LS Lossless
- JPEG-LS Near Lossless
- JPEG 2000
- JPEG 2000 Lossless
- RLE Lossless

### Parallel Compression

- Persistent worker thread pool
- Chunk-based compression
- Thread-local z_stream reuse
- Adler-32 accumulation and combination
- Compatible with zlib-ng

### Multi-Frame DICOM Export

- Single frame export
- Specific frame export via `--frame`
- Entire volume export via `--all`

---

## Architecture

```text
Image Input
    │
    ▼
Loader Thread
    │
    ▼
Queue A
    │
    ▼
CUDA Processing
    │
    ▼
Queue B
    │
    ▼
DEFLATE Thread Pool
    │
    ▼
Queue C
    │
    ▼
PNG Writer
    │
    ▼
PNG Output
```

---

## Compression Backend

This project uses **zlib-ng** through the zlib compatibility API.

### Current Defaults

```cpp
Threads = 6
Compression Level = 3
Strip Height = 256
```

### Optimization Techniques

- Raw DEFLATE (`windowBits = -15`)
- `Z_FULL_FLUSH` between intermediate chunks
- Single final `Z_FINISH`
- Thread-local compressor reuse
- Parallel chunk scheduling

---

## Build Requirements

- CUDA Toolkit 11.8+
- DCMTK
- OpenJPEG
- zlib-ng
- libpng
- LibRaw
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

### Examples

```bash
gpu_png_encoder study.dcm output_folder --all
gpu_png_encoder input.dcm output.png --frame 12
gpu_png_encoder input.dcm output.png --threads 8
gpu_png_encoder input.dcm output.png --level 0
gpu_png_encoder input.dcm output.png --verbose
```

---

## DEFLATE Benchmark

```bash
gpu_png_encoder --bench-deflate
```

---

## Project Structure

```text
include/
src/
CMakeLists.txt
vcpkg.json
README.md
```

---

## Performance Goals

- High throughput
- Efficient CPU/GPU utilization
- Low memory consumption
- Fast PNG generation
- Scalable processing for large medical datasets

---

## Future Improvements

- Pinned host memory
- CUDA streams
- Multi-GPU support
- GPU PNG filtering
- Batch processing mode
- Benchmark dashboard
- PACS integration

---

## License

This project is intended for research, learning, and high-performance image processing experimentation.
