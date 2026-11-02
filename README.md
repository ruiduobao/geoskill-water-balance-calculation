# Water Balance Calculation (geoskill-water-balance-calculation)

> Per-pixel water balance computation P = ET + Q + ΔS with closure residual assessment. Outputs per-component/closure-residual GeoTIFFs + a report JSON.

---

## 1. Overview

Per-pixel water balance computation, with the core equation: P = ET + Q + ΔS, i.e. **precipitation = evapotranspiration + runoff + storage change**. Each component is computed independently per pixel, and the closure residual is derived as: residual = P − ET − Q − ΔS. The residual is 0 under ideal closure; real observation data contain residuals because each component comes from a different data source (precipitation gauge networks, remote-sensing evapotranspiration products, hydrological model runoff, gravity-satellite storage change). This skill uses the relative closure error (mean|residual| / mean P) to quantify data consistency, a common approach for diagnosing the quality of multi-source hydrological data and identifying systematic biases. It is suitable for basin water balance diagnosis, cross-validation of remote-sensing products, and quality assessment before data assimilation. The `--synthetic` mode generates a complete, physically closed dataset (ET≈0.45P, Q≈0.30P, ΔS as the closing residual, with an added observational perturbation of std≈3 mm), so the workflow can be verified without network access or real data; the relative closure error of synthetic data should be < 2%.

## 3. Quick Start

```bash
pip install -r requirements.txt
python geoskill-water-balance-calculation.py --bbox 116 39 117 40 --synthetic --output-dir ./out
```

## 4. CLI Parameters

Run `python <skill>.py --help` for the full list. Common parameters:

| Parameter | Type | Description |
|---|---|---|
| `--bbox` | `float[4]` | WGS84 bounding box `min_lon min_lat max_lon max_lat` |
| `--input` | `path` | Local input file (GeoJSON/GeoTIFF/etc.) |
| `--output-dir` | `path` | Output directory (default `./output`) |
| `--synthetic` | `flag` | Use synthetic data instead of real input |
| `--quiet` | `flag` | Suppress non-essential stdout |

## 5. Input / Output

| File | Format | Description |
|---|---|---|
| `balance_components.tif` | GeoTIFF (float32, 4 bands) | Component raster: B1=P, B2=ET, B3=Q, B4=ΔS, EPSG:4326 |
| `closure_residual.tif` | GeoTIFF (float32) | Closure residual P−ET−Q−ΔS |
| `water_balance_report.json` | JSON | Per-component means, closure residual mean/std, relative closure error, volumetric quantities |
| `output-manifest.json` | JSON | Run manifest (inputs/outputs/QA/software versions) |

## 6. Technical Principle

(see SKILL.md for details)

## 8. License

MIT License. See [`LICENSE`](./LICENSE) for full text.

---

> 中文完整版：[references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)
