# VISTA-Bench

<p align="center">
  <img src="assets/figures/motivation.jpg" width="980" alt="VISTA-Bench motivation">
</p>
<p align="center">
  <strong>Do VLMs really understand visualized text as well as pure text?</strong><br/>
  A systematic benchmark spanning <em>multimodal perception → reasoning → unimodal knowledge</em>.
</p>

<p align="center">
  <a href="https://arxiv.org/abs/2602.04802">
    <img alt="arXiv" src="https://img.shields.io/badge/arXiv-2602.04802-b31b1b.svg">
  </a>
  <img alt="License" src="https://img.shields.io/badge/license-TBD-lightgrey">
  <img alt="Dataset" src="https://img.shields.io/badge/dataset-1500%20instances-blue">
  <img alt="Tasks" src="https://img.shields.io/badge/tasks-MCQ%20%2B%20Open--ended-orange">
  <img alt="Eval" src="https://img.shields.io/badge/eval-VLMEvalKit-green">
</p>

<p align="center">
  <em>Figure 1: Motivation of VISTA-Bench.</em>
</p>

---

## Overview

We introduce **VISTA-Bench**, a systematic benchmark spanning multimodal perception, reasoning, and unimodal understanding. It evaluates *visualized text understanding* by contrasting **pure-text** and **visualized-text (VT)** questions under controlled rendering conditions.

---

## Dataset at a glance

- **Size:** 1,500 instances
- **Composition:** predominantly multiple-choice questions (MCQ), with a small portion of open-ended queries
- **Task taxonomy (4 primary categories):**
  - **Unimodal Knowledge:** 500
  - **Multimodal Knowledge:** 400
  - **Multimodal Perception:** 300
  - **Multimodal Reasoning:** 300

<p align="center">
  <img src="assets/figures/ability_dimension.jpg" width="500" alt="VISTA-Bench ability dimensions">
</p>
<p align="center">
  <em>Figure 2: Ability dimensions and task taxonomy of VISTA-Bench.</em>
</p>

---

## Qualitative example

<p align="center">
  <img src="assets/figures/unimodal-knowledge-2.svg" width="500" alt="VISTA-Bench uni-knowledge case">
</p>

<p align="center">
  <img src="assets/figures/multi-reasoning-2.svg" width="500" alt="VISTA-Bench multi-reasoning case">
</p>

<p align="center">
  <em>Figure 3: A representative multimodal perception case under the visualized-text interface.</em>
</p>

---

## Repository structure

```text
VISTA-Bench/
├─ assets/figures/    # figures used in this README
├─ images/            # original images (for multimodal instances)
├─ questions/         # rendered question/option images (VT setting)
├─ VLMEvalKit/        # evaluation toolkit
├─ VISTA-Bench.tsv    # dataset index (currently identical to the VT variant)
└─ VISTA-Bench-VT.tsv # dataset index (currently identical; kept for compatibility)
```

> **Note:** `VISTA-Bench.tsv` and `VISTA-Bench-VT.tsv` are currently identical; we keep both filenames for compatibility and will refine the organization later.

---

## Data format

- `images/`: original images used by multimodal instances
- `questions/`: rendered question/option images for the visualized-text (VT) setting
- `*.tsv`: dataset metadata and file paths used for evaluation

---

## Evaluation (VLMEvalKit)

We evaluate VISTA-Bench with `VLMEvalKit/`.  
Before running evaluation, we recommend converting the TSV file(s) into a normalized format with absolute image paths.

### 1) Convert TSV to normalized paths

Helper script:
- `VISTA-Bench/VLMEvalKit/utils/convert_data_file.py`

What it does:
- converts the TSV encoding to UTF-8
- normalizes path separators (`\` → `/`)
- renames `options_A/B/C/D` → `A/B/C/D` when needed
- converts `image_path` and `question_image_path` into a bracket-style multi-path string

Example:

```bash
python VISTA-Bench/VLMEvalKit/utils/convert_data_file.py \
  --in   VISTA-Bench/VISTA-Bench.tsv \
  --out  VISTA-Bench/VISTA-Bench_norm.tsv \
  --image-prefix /ABS/PATH/TO/VISTA-Bench \
```

- `--in`: input TSV path (the original dataset TSV to be converted)
- `--out`: output TSV path (the converted/normalized TSV produced by this script)
- `--image-prefix`: the dataset root directory where images/, questions/, and the TSV files are located (used to resolve relative paths)

After conversion, rename the TSV column header `image` to `image-1` to avoid an AssertionError in some VLMEvalKit setups.

### 2) Run evaluation

**Pure-text:**
```bash
python /VISTA-Bench/VLMEvalKit/run.py \
  --data VISTA-Bench_norm \
  --model llava_v1.5_7b \
  --verbose
```

**Visualized-text (VT):**
```bash
python /VISTA-Bench/VLMEvalKit/run.py \
  --data VISTA-Bench-VT \
  --model llava_v1.5_7b \
  --verbose
```

- `--data`: the dataset name corresponding to your converted TSV (e.g., `VISTA-Bench_norm`). The VT split (`VISTA-Bench-VT`) should also be converted and can be run directly using this name because it is registered in `VLMEvalKit/vlmeval/dataset/image_mm_mcq.py` via `DATASET_URL/MD5`.
- `--model`: the model name defined in `VLMEvalKit/vlmeval/config.py` (make sure the corresponding weights are available in your environment).
- `--verbose`: print detailed logs during evaluation.
- Outputs: the final report includes `overall` results and `l1-categories` breakdown.

---

## Results

<div align="center">
<table style="width:100%; border-collapse:collapse; font-size:14px; line-height:1.25;">
  <thead>
    <tr>
      <th colspan="12" style="border:1px solid #cfd7e3; padding:12px 10px; text-align:center; font-size:20px; font-weight:700;">
        Modality Comparison (VT vs. Text)
      </th>
    </tr>
    <tr>
      <th rowspan="3" style="border:1px solid #cfd7e3; padding:10px; text-align:left; width:28%;">Model</th>
      <th colspan="2" style="border:1px solid #cfd7e3; padding:10px; text-align:center;">Multimodal</th>
      <th colspan="2" style="border:1px solid #cfd7e3; padding:10px; text-align:center;">Multimodal</th>
      <th colspan="2" style="border:1px solid #cfd7e3; padding:10px; text-align:center;">Multimodal</th>
      <th colspan="2" style="border:1px solid #cfd7e3; padding:10px; text-align:center;">Unimodal</th>
      <th colspan="2" rowspan="2" style="border:1px solid #cfd7e3; padding:10px; text-align:center;">Overall</th>
      <th rowspan="3" style="border:1px solid #cfd7e3; padding:10px; text-align:center; width:7%;">↓ Gap</th>
    </tr>
    <tr>
      <th colspan="2" style="border:1px solid #cfd7e3; padding:10px; text-align:center;">Perception</th>
      <th colspan="2" style="border:1px solid #cfd7e3; padding:10px; text-align:center;">Reasoning</th>
      <th colspan="2" style="border:1px solid #cfd7e3; padding:10px; text-align:center;">Knowledge</th>
      <th colspan="2" style="border:1px solid #cfd7e3; padding:10px; text-align:center;">Knowledge</th>
    </tr>
    <tr>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center;">VT</th>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">Text</th>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center;">VT</th>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">Text</th>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center;">VT</th>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">Text</th>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center;">VT</th>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">Text</th>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center;">VT</th>
      <th style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">Text</th>
    </tr>
  </thead>
    <tbody>
      <tr>
        <td colspan="12" style="border:1px solid #cfd7e3; padding:10px; background:#ebf5ff; font-weight:700;">
          ▼ Closed-source Vision-Language Models
        </td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Gemini-3.1-Pro-Preview</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>72.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>74.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>73.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>76.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>80.5</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>82.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>89.4</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>90.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>80.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>81.9</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f8f1f1; color:#c80000; font-weight:600;">↓ -1.6</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">GPT-5.2</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>57.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>67.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>53.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>63.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>46.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>58.8</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>70.2</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>83.2</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>57.8</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>69.5</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e7caca; color:#c80000; font-weight:600;">↓ -11.7</td>
      </tr>
      <tr>
        <td colspan="12" style="border:1px solid #cfd7e3; padding:10px; background:#ebf5ff; font-weight:700;">
          ▼ Large-scale Vision-Language Models
        </td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Qwen3.5-122B-A10B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>71.7</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>74.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>72.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>74.7</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>67.8</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>78.8</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>86.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>86.6</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>75.5</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>79.6</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f4e7e7; color:#c80000; font-weight:600;">↓ -4.1</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">GLM-4.6V</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>70.3</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>71.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>67.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>68.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>63.3</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>69.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>80.8</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>82.6</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>71.3</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>73.9</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f7eded; color:#c80000; font-weight:600;">↓ -2.6</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">InternVL3.5-241B-A28B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">68.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">67.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">56.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">67.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">43.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">61.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">65.2</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">82.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">58.2</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">71.9</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e4c2c2; color:#c80000; font-weight:600;">↓ -13.7</td>
      </tr>
      <tr>
        <td colspan="12" style="border:1px solid #cfd7e3; padding:10px; background:#ebf5ff; font-weight:700;">
          ▼ Vision-Language Models (30B)
        </td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Qwen3-VL-30B-A3B-Instruct</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>64.3</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>71.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>51.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">60.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">35.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>58.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">54.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>75.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">50.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>66.7</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e0b9b9; color:#c80000; font-weight:600;">↓ -15.9</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">InternVL3.5-30B-A3B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>64.3</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">70.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">50.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>61.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>41.8</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>52.5</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>61.8</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>75.2</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>54.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>65.5</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e9cdcd; color:#c80000; font-weight:600;">↓ -10.8</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Kimi-VL-A3B-Thinking</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>70.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>71.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>52.7</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>66.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>41.8</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">43.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>59.4</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">68.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>55.5</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">62.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f0dede; color:#c80000; font-weight:600;">↓ -6.5</td>
      </tr>
      <tr>
        <td colspan="12" style="border:1px solid #cfd7e3; padding:10px; background:#ebf5ff; font-weight:700;">
          ▼ Vision-Language Models (8B)
        </td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">GLM-4.1V-9B-Thinking</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>70.7</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>71.3</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">58.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">61.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>51.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>57.5</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>73.8</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>75.8</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>64.1</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>67.2</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f6ebeb; color:#c80000; font-weight:600;">↓ -3.1</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Ovis2.5-9B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">68.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">69.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">56.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>65.7</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">40.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>56.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">66.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">73.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">57.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>66.4</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#ecd5d5; color:#c80000; font-weight:600;">↓ -8.7</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">MiMo-VL-7B-SFT</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>68.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">69.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>61.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>63.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>46.5</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">47.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>71.4</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>76.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>62.2</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">64.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f7eeee; color:#c80000; font-weight:600;">↓ -2.3</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">SAIL-VL2-8B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>68.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">70.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">54.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">60.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">37.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">45.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">58.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">71.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">54.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">62.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#eed8d8; color:#c80000; font-weight:600;">↓ -8.0</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">MiMo-VL-7B-RL</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>70.7</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">69.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>59.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">62.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">45.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">44.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">70.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">71.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">61.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">61.9</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#faf6f6; color:#c80000; font-weight:600;">↓ -0.3</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">LLaVA-OneVision-1.5-8B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">62.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">68.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">46.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">59.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">34.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">44.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">57.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">67.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">50.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">60.1</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#ead0d0; color:#c80000; font-weight:600;">↓ -10.1</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">NEO-9B-SFT</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">32.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">69.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">29.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">58.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">25.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">41.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">28.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">69.2</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">28.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">59.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#c77f7f; color:#c80000; font-weight:600;">↓ -31.0</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">InternVL3.5-8B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">61.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">64.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">45.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">52.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">36.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">45.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">57.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">71.2</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">50.2</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">59.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#ecd4d4; color:#c80000; font-weight:600;">↓ -9.1</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Qwen3-VL-8B-Instruct</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">65.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">67.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">49.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">49.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">37.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">48.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">58.2</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">68.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">52.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">59.1</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f0dddd; color:#c80000; font-weight:600;">↓ -6.7</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Ovis2-8B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">66.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">71.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">47.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">60.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">29.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">41.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">50.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">65.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">47.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">58.9</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8cbcb; color:#c80000; font-weight:600;">↓ -11.3</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Qwen2.5-VL-7B-Instruct</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">65.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">65.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">52.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">53.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">27.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">37.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">62.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">62.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">51.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">54.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f6eded; color:#c80000; font-weight:600;">↓ -2.7</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">MiniCPM-V-4_5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">64.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>71.6</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">45.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">60.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">31.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">36.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">50.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">55.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">47.2</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">54.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#efdcdc; color:#c80000; font-weight:600;">↓ -7.1</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">LLaVA-OneVision-7B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">40.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">66.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">27.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">56.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">20.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">35.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">27.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">58.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">28.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">53.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#d09494; color:#c80000; font-weight:600;">↓ -25.5</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">LLaVA-1.5-7B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">33.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">58.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">28.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">44.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">27.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">28.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">24.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">48.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">27.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">44.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#dfb6b6; color:#c80000; font-weight:600;">↓ -16.7</td>
      </tr>
      <tr>
        <td colspan="12" style="border:1px solid #cfd7e3; padding:10px; background:#ebf5ff; font-weight:700;">
          ▼ Vision-Language Models (2B / 3B)
        </td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Ovis2.5-2B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>66.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>70.0</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>51.7</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>58.7</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">29.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>43.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>51.8</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>60.4</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>48.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><strong>57.4</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#ecd5d5; color:#c80000; font-weight:600;">↓ -8.7</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">SAIL-VL2-2B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>65.3</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>69.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>47.3</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>57.7</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><u>32.8</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">40.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">43.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">54.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">45.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>54.5</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#ecd5d5; color:#c80000; font-weight:600;">↓ -8.7</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">InternVL3.5-2B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">56.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">66.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">39.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">50.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">30.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>41.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">45.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;"><u>57.0</u></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">42.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">53.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e9cdcd; color:#c80000; font-weight:600;">↓ -10.9</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Qwen2.5-VL-3B-Instruct</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">65.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">67.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">43.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">54.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>33.3</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">36.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>54.8</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">56.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;"><strong>48.8</strong></td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">52.9</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f4e7e7; color:#c80000; font-weight:600;">↓ -4.1</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">NEO-2B-SFT</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">40.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">68.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">31.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">49.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">25.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">38.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">29.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">53.4</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">30.9</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">51.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#d8a7a7; color:#c80000; font-weight:600;">↓ -20.7</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Qwen3-VL-2B-Instruct</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">56.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">69.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">41.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">49.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">26.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">27.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">49.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">56.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">43.1</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">49.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#f0dede; color:#c80000; font-weight:600;">↓ -6.5</td>
      </tr>
      <tr style="background:#ffffff;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">Ovis2-2B</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">58.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">66.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">39.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">52.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">27.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">31.5</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">36.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">50.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">39.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">49.1</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#ead0d0; color:#c80000; font-weight:600;">↓ -10.1</td>
      </tr>
      <tr style="background:#f7f9fb;">
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:left;">DeepSeek-VL2-Tiny</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">44.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">64.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">31.3</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">41.0</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">27.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">29.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">27.6</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">41.8</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center;">31.7</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8e8e8;">42.9</td>
        <td style="border:1px solid #cfd7e3; padding:10px; text-align:center; background:#e8cccc; color:#c80000; font-weight:600;">↓ -11.1</td>
      </tr>
  </tbody>
</table>
<p style="margin-top:8px; color:#555;">
  <em>
    Caption: Comparison of different VLMs on our benchmark. Results are reported under <strong>Visualized Text (VT)</strong> and <strong>Text</strong> inputs for each metric.
    The <strong>best</strong> result per column is bolded.
    The ↓Gap column denotes the overall performance drop when switching from Text to Visualized Text. All metrics are reported as percentages (%).
  </em>
</p>
</div>

## Contact

- Qing'an Liu: <2223884741@mail.dlut.edu.cn>
- Juntong Feng: <2253762636@mail.dlut.edu.cn>

## Citation

To be added.

---

## License

To be added.
