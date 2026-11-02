---
name: geoskill-water-balance-calculation
description: '逐像元水量平衡计算 P = ET + Q + ΔS，评估闭合差。Per-pixel water balance computation P = ET + Q + ΔS with closure residual assessment. 输出各分量/闭合差 GeoTIFF + 报告 JSON。'
---

# 水量平衡计算 | Water Balance Calculation

Per-pixel water balance computation, with the core equation:

    P = ET + Q + ΔS

i.e. **Precipitation = Evapotranspiration + Runoff + Change in Storage**. For each pixel, every component is computed independently and the closure residual is derived:

    residual = P − ET − Q − ΔS

Under perfect closure the residual is 0; with real observational data a residual remains because the components come from different sources (precipitation station networks, remote-sensing evapotranspiration products, hydrological model runoff, gravity-satellite storage change). This skill uses the relative closure error (mean|residual| / mean P) to quantify data consistency — a common way to diagnose the quality of multi-source hydrological data and identify systematic biases, applicable to watershed water-balance diagnosis, cross-validation of remote-sensing products, and quality assessment prior to data assimilation.

The `--synthetic` mode generates a complete dataset that is physically closed (ET≈0.45P, Q≈0.30P, ΔS as the closing residual, plus observational perturbation with std≈3 mm), so the pipeline can be validated without network access or real data; the relative closure error of synthetic data should be < 2%.

## Dependencies / 依赖

```bash
pip install numpy rasterio scipy
```

## Usage / 使用方法

### Basic Usage (Synthetic Data, Offline)

```bash
python geoskill-water-balance-calculation.py --bbox 116.0 39.0 117.0 40.0 --output-dir ./output
```

### Example 1: Synthetic Data Water Balance

```bash
python geoskill-water-balance-calculation.py \
    --bbox 116.0 39.0 117.0 40.0 \
    --synthetic \
    --output-dir ./wb_syn
```

### Example 2: Real Precipitation Raster (Demo Mode)

```bash
python geoskill-water-balance-calculation.py \
    --input precip_annual.tif \
    --output-dir ./wb_real
```

(With the input precipitation as P, the remaining components are synthesized in empirical proportions to demonstrate the workflow.)

### Example 3: Different Regions

```bash
python geoskill-water-balance-calculation.py --bbox 121 31 122 32 --synthetic --output-dir ./wb_sh --quiet
```

### Example 4: Minimal Region

```bash
python geoskill-water-balance-calculation.py --bbox 116.39 39.90 116.40 39.91 --synthetic --output-dir ./wb_tiny --quiet
```

## Output / 输出

| File | Format | Description |
|---|---|---|
| `balance_components.tif` | GeoTIFF (float32, 4 bands) | Component rasters: B1=P, B2=ET, B3=Q, B4=ΔS, EPSG:4326 |
| `closure_residual.tif` | GeoTIFF (float32) | Closure residual P−ET−Q−ΔS |
| `water_balance_report.json` | JSON | Component means, closure residual mean/std, relative closure error, volumetric quantities |
| `output-manifest.json` | JSON | Run manifest (inputs/outputs/QA/software versions) |

## Data Source / 数据源 / Source

- **Precipitation P**: local GeoTIFF, or from CHIRPS / GPM / ground stations
- **ET / Q / ΔS**: generated in synthetic mode; real applications can integrate MODIS ET, hydrological models, GRACE, etc.
- **Synthetic mode**: generated locally, no external data source

## Privacy / 隐私声明 / Privacy

- Fully offline by default; no network requests are made
- `--synthetic` mode reads no external data
- All computation is done locally; no user data is uploaded

## License / License

MIT

---

> 中文完整版：需要时再读 [references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)，不要默认全量加载。
