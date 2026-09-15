# Apple seedling RGB, thermal infrared, and 3D temperature data

This release contains **five distinct apple seedlings** acquired on **13 July 2026**: `R1L1`, `R1L2`, `R1L4`, `R2L1`, and `R2L2`. Each plant has **90 temporally paired RGB and thermal frames**, together with its existing final thermal point cloud. There are **450 frame pairs and five point clouds** in total.

The plants are the first five plant IDs in alphanumeric order for this acquisition batch with complete files and verified correspondence to the archived RGB frames. `R1L3` was excluded because sequential decoding at some recorded RGB indices did not agree with the archived images. Selection was based on source correspondence and availability, not registration accuracy or temperature coverage. This is a subset of the study data, not the complete study dataset.

## Contents

```text
samples/2026-07-13/<plant_id>/
  rgb/                         RGB frames, lossless PNG
  thermal_raw16/               Native thermal detector values, uint16 TIFF
  thermal_temp_c/              Calibrated temperature, float32 TIFF, degrees Celsius
  frame_pairs.csv              Explicit thermal-to-RGB frame correspondence
  radiometric_metadata.json    Radiometric parameters and source recording hashes
  point_cloud/
    <plant_id>_thermal_point_cloud.ply
    point_cloud_metadata.json
samples.csv                    Plant-level inventory and point counts
checksums.sha256               SHA-256 checksums for released files
```

## Image provenance and correspondence

- RGB frames were recovered from the original MP4 recordings using the **exact zero-based RGB frame indices** in the archived pairing tables. They are native decoded video frames (1080 pixels wide by 1920 pixels high), saved as lossless PNG without masking, resizing, or geometric registration. The PNGs are newly exported frames, not byte-identical copies of previously exported JPEGs.
- Thermal frames were recovered from the original FLIR SEQ recordings at the paired **zero-based thermal frame indices**. Both thermal TIFF representations have the native detector dimensions of 640 pixels wide by 480 pixels high, without masking or geometric registration.
- `thermal_raw16` preserves the original 16-bit detector values. These values are **not temperatures in Celsius**.
- `thermal_temp_c` contains the corresponding calibrated float32 temperatures, using the SEQ radiometric parameters and the same emissivity/atmosphere correction as the archived export. These are quantitative temperature images, not pseudocolor pictures. Each thermal observation is supplied in these two representations; it must not be counted twice.
- Each `frame_pairs.csv` gives the actual frame indices, the recorded thermal timestamp, the RGB video time, and relative file paths. Pairing uses the original manual-anchor mapping; it does not establish hardware synchronization. A matching pair is a temporal correspondence, not pixelwise geometric alignment.
- Source recording SHA-256 hashes and calibration parameters are retained in `radiometric_metadata.json`. The original MP4/SEQ recordings themselves are not part of this image subset.

## Thermal point clouds

The PLY files are **byte-identical copies of the existing final thermal-fusion outputs** for the same plants and acquisition date. They were not regenerated for this release. They preserve geometry, normals, original colors, temperature values, and all additional per-point fields.

Key properties:

| Property | Meaning |
|---|---|
| `x`, `y`, `z` | Coordinates in the native reconstruction coordinate system |
| `nx`, `ny`, `nz` | Surface normals |
| `temperature_c` | Temperature in degrees Celsius; unassigned points retain NaN |
| `temperature_source` | `0`: unassigned; `1`: direct multiview fusion; `2`: spatial interpolation |
| `temperature_views` | Number of accepted supporting views |
| `temperature_candidate_views` | Number of candidate supporting views |
| `temperature_confidence` | Confidence field from the existing fusion pipeline |
| `temperature_std_c` | Temperature dispersion field from the existing fusion pipeline |
| `temperature_angle_sectors` | Angular-support field from the existing fusion pipeline |
| `red`, `green`, `blue` | Visualization colors stored in the final PLY |
| `original_red`, `original_green`, `original_blue` | Preserved original point colors |

Coordinate scale is retained as stored; this release does not establish a conversion to meters or millimeters. Point colors should not be used to recover temperature when the numeric `temperature_c` field is available. Direct-fusion coverage and interpolated coverage are reported separately in `samples.csv` and the point-cloud metadata.

## Download

Binary image and point-cloud files are stored using **Git LFS**. Install Git and Git LFS, then run:

```bash
git lfs install
git clone https://github.com/doudou1314520/RGB-and-thermal-infrared-registered-image-dataset-of-apple-seedlings.git
cd RGB-and-thermal-infrared-registered-image-dataset-of-apple-seedlings
git lfs pull
```

Use a Git LFS checkout to ensure that the actual data files are downloaded. Small text files beginning with `version https://git-lfs.github.com/spec/v1` are pointers, not image or point-cloud data.

## Validation and scope

All released images were read back and checked for exact array equality against their extracted data. For all 450 RGB frames, the original JPEG-quality-95 encoding was recreated in memory; the resulting pixel values exactly matched every fully opaque pixel in the archived masked RGB images. The released PNGs retain the lossless decoded frames. Calibrated thermal minimum, maximum, and mean values were checked against the archived export for every selected frame. All five PLY files were parsed to verify their vertex counts, temperature fields, and assignment flags against their original summaries. Source-file hashes were checked before and after preparation, and copied files were checked by SHA-256.

The 450 views belong to **five biological plants**, not 450 independent biological samples. The point clouds are derived outputs. This release supplies paired native images and final point clouds; it does not include all calibration, segmentation, camera-pose, and registration intermediates required to reproduce the complete pipeline.

## 中文说明

本次公开 2026 年 7 月 13 日采集的 5 株苹果幼苗（R1L1、R1L2、R1L4、R2L1、R2L2），每株 90 对 RGB—热红外图像，共 450 对，以及对应的 5 个最终温度点云。

RGB 和热红外图像按已有逐帧配对表从原始 MP4/SEQ 中恢复，未进行掩膜、缩放或几何配准。热红外同时提供 16 位原始探测器数据和摄氏温度 Float32 TIFF；二者是同一观测的两种表示。最终 PLY 为原文件完整复制，保留 `temperature_c` 和 `temperature_source` 等字段。配对图像属于时间对应，不表示像素已对齐。请使用 Git LFS 下载实际数据。
