# Video quality benchmark tool - VMAF, PSNR & SSIM (Python + FFmpeg)

[![Python 3.9+](https://img.shields.io/badge/Python-3.9%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![FFmpeg + libvmaf](https://img.shields.io/badge/FFmpeg-libvmaf-007808?logo=ffmpeg&logoColor=white)](https://ffmpeg.org/)
[![Metrics: VMAF · PSNR · SSIM](https://img.shields.io/badge/metrics-VMAF%20%C2%B7%20PSNR%20%C2%B7%20SSIM-0E7C66)](#features)

Measure objective video quality between a **source** and an **encoded** video, frame by frame. This tool reports **VMAF, PSNR, and SSIM**, plus edge-retention, noise, temporal-flicker, motion-stability, and audio-sync analysis, and writes the results as JSON - built with Python and FFmpeg (libvmaf). It drops straight into encoder comparison, transcoding validation, video-compression benchmarking, and quality-regression testing.

**Works with:** Python 3.9+ · FFmpeg (libvmaf) · VMAF / PSNR / SSIM · MP4 in, JSON out · macOS & Linux

📖 **How it works:** https://fastpix.com/blog/fastpix-video-quality-analysis-frame-level-precision-across-every-pixel &nbsp;·&nbsp; 🌐 **FastPix:** https://www.fastpix.com

A frame-level video quality benchmarking tool built using Python + FFmpeg (libvmaf).

This tool performs deep objective quality analysis between a **source** video and an **encoded** video.

---

## Features

### Video Quality Metrics
- VMAF (Average, Min, Std Dev, Worst Frame)
- PSNR (Average, Std Dev, Worst Frame)
- Global SSIM
- Frame-level SSIM analysis

### Advanced Frame Analysis
- SSIM variance tracking
- Edge retention ratio & variance
- Noise difference & variance
- Temporal flicker score & variance
- Motion stability score & variance

### Audio Analysis
- Audio sync lag (samples)
- Audio sync lag (milliseconds)

---

## Requirements

- Python 3.9+
- FFmpeg compiled with libvmaf

Verify that FFmpeg was built with libvmaf:

```bash
ffmpeg -filters | grep vmaf
```

Expected output should include:

```text
libvmaf           VV->V      Calculate the VMAF between two video streams.
```

If the output only shows `vmafmotion` and not `libvmaf`, FFmpeg does not have VMAF support enabled and the benchmark will not run correctly.

You can also verify directly:

```bash
ffmpeg -h filter=libvmaf
```

This command should return information about the `libvmaf` filter.


If missing (macOS):

```bash
brew install ffmpeg
```

To install FFmpeg with VMAF support on macOS:

```bash
brew install libvmaf
brew reinstall ffmpeg
```

Verify the installation:

```bash
ffmpeg -filters | grep vmaf
```

Expected output should include:

```text
libvmaf           VV->V      Calculate the VMAF between two video streams.
```

Or, for other platforms, follow the official FFmpeg documentation to enable libvmaf during compilation.

---

## Installation

```bash
git clone https://github.com/FastPix/video-quality-benchmark-tool.git
cd video-quality-benchmark-tool

python3 -m venv venv
source venv/bin/activate

pip install -r requirements.txt
```

### Verify you are in the repository root

Before running any commands, verify that you are inside the repository directory:

```bash
pwd
ls
```

You should see files similar to:

```text
README.md
requirements.txt
video_benchmark.py
source/
encoded/
```

If `requirements.txt` cannot be found, make sure you are inside the `video-quality-benchmark-tool` directory before continuing.

---

## Input File Structure

This repository includes two folders with demo video files:

```
source/
encoded/
```

- `source/` contains a sample reference video.
- `encoded/` contains a sample encoded/transcoded video.

**Note:** These are demo files for testing. For real benchmarking, upload your own videos to these folders and rename them as described below.

### Using Your Own Videos

To run the benchmark on your own videos:


1. Replace the demo file inside `source/` with your reference video.
2. Replace the demo file inside `encoded/` with your encoded/transcoded video.
3. **Rename your files to match the existing filenames.**

Example structure:

```
source/source.mp4
encoded/encoded.mp4
```


**Important:**
- Keep the same filenames (`source.mp4` and `encoded.mp4`)
- Both videos should ideally have the same resolution
- Both videos should have the same frame rate
- Both videos should have similar duration

---

## Notes

- **Processing Time:** Large video files will take significantly longer to process and analyze. Processing time depends on video length, resolution, and system performance.
- **File Names:** Always rename your uploaded videos to `source.mp4` and `encoded.mp4` for the tool to work correctly.
- **Demo Videos:** The `source/` and `encoded/` folders contain demo videos. Replace them with your own for real benchmarking.


---

## Run Benchmark

```bash
python video_benchmark.py \
  --source source/source.mp4 \
  --encoded encoded/encoded.mp4 \
  --output result.json
```

---

## 📊 Example Output

```json
{
  "video_quality": {
    "vmaf_average": 97.30,
    "vmaf_min": 81.30,
    "vmaf_std": 2.54,
    "vmaf_worst_frame_index": 59,
    "psnr_average": 48.71,
    "psnr_std": 5.08,
    "psnr_worst_frame": 38.74,
    "psnr_worst_frame_index": 893
  },
  "frame_analysis": {
    "avg_frame_ssim": 0.9865,
    "min_frame_ssim": 0.9533,
    "ssim_std": 0.0071,
    "edge_retention_ratio": 0.955,
    "edge_variance": 75.48,
    "avg_noise_difference": -1.32,
    "noise_variance": 1.02,
    "temporal_flicker_score": 1.64,
    "flicker_variance": 6.54,
    "motion_stability_score": 0.845,
    "motion_variance": 0.125
  },
  "audio_sync": {
    "lag_samples": 0,
    "lag_ms": 0.0
  }
}
```

---

## 🔍 Use Cases

- Encoder comparison (AWS / GCP / custom pipeline)
- Transcoding validation
- Video compression benchmarking
- Quality regression testing
- Detecting flicker and motion instability

---

## FAQ

**How do I measure VMAF between two videos?**

Install FFmpeg with libvmaf, then run `video_benchmark.py` with `--source` and `--encoded` (see [Run Benchmark](#run-benchmark)). The JSON output reports VMAF average, min, standard deviation, and the worst-frame index.

**Does it also report PSNR and SSIM?**

Yes. The same run reports PSNR (average, std dev, worst frame) and SSIM (global and frame-level) alongside VMAF. See [Example Output](#-example-output).

**Do I need FFmpeg compiled with libvmaf?**

Yes - VMAF requires libvmaf. Verify with `ffmpeg -filters | grep vmaf` (see [Requirements](#requirements)). On macOS, install FFmpeg via Homebrew.

**How do I benchmark an encoder or transcoding pipeline?**

Encode the same source through each encoder or pipeline, then benchmark every encoded output against the original source and compare the VMAF/PSNR/SSIM scores. See [Use Cases](#-use-cases).

**Can I run it on my own videos?**

Yes. Replace the demo files in `source/` and `encoded/` with your reference and encoded videos, keeping the filenames `source.mp4` and `encoded.mp4`. See [Using Your Own Videos](#using-your-own-videos).

**What do the two videos need to have in common?**

Use MP4 files named `source.mp4` and `encoded.mp4`; both should ideally share the same resolution and frame rate and have similar duration. See [Input File Structure](#input-file-structure).

**How do I detect flicker or motion instability?**

The `frame_analysis` output includes a temporal-flicker score and a motion-stability score (with variances). See [Example Output](#-example-output).

**Does it check audio sync?**

Yes. The `audio_sync` output reports lag in samples and milliseconds. See [Example Output](#-example-output).

**Why does it take a long time on large files?**

Processing time scales with video length, resolution, and system performance. See [Notes](#notes).

---

## More from FastPix

Built by [FastPix](https://www.fastpix.com). For the story behind the metrics, read [FastPix video quality analysis - frame-level precision across every pixel](https://fastpix.com/blog/fastpix-video-quality-analysis-frame-level-precision-across-every-pixel). 

Explore more open-source tools and SDKs in the [FastPix organization](https://github.com/orgs/FastPix/repositories).
