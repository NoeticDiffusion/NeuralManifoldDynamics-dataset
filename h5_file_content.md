# HDF5 File Content and Metadata Guide

The core output artifacts of the NeuralManifoldDynamics (v2.0) measurement contract are self-describing HDF5 (`.h5`) files. These files contain the standardized, low-dimensional trajectory summaries and their associated local dynamics (Jacobians), organized with explicit naming conventions to guarantee clear export semantics and reproducibility.

## HDF5 Internal Structure

A standard generated HDF5 file in this dataset contains the following key groups and paths:

### 1. Canonical Trajectories
- `/mnps_3d`: The canonical three-dimensional trajectory, exported with a fixed axis order `[m, d, e]` (metastability, deviation, entropy). 
- `/mnps_3d_dot`: The temporal derivative of the canonical 3D trajectory.

### 2. Stratified Trajectories
- `/coords_9d/values`: The stratified coordinate matrix containing the nine subcoordinates that refine the canonical 3D chart.
- `/coords_9d/names`: The corresponding axis names for the 9D chart (e.g., `m_a`, `m_e`, `d_n`, etc.).

### 3. Local Dynamics (Jacobians)
- `/jacobian/J_hat`: Primary 3D Jacobians computed on `mnps_3d`.
- `/jacobian_9D/J_hat`: Stratified Jacobians computed on `coords_9d` (when enabled and supported by the modality).

### 4. Regional Manifold Dynamics
- `/regional_mnps/<network_or_channel_group>/mnps`: Regional 3D trajectories computed separately for specific fMRI networks or EEG channel groups.
- `/regional_mnps/<network_or_channel_group>/jacobian`: Regional Jacobians for the specific group.

### 5. Features and Tabular Extensions
- `/features_raw/*`: The absolute scale untransformed features.
- `/features_robust_z/*`: A strict robust-z transformed view of the raw feature matrix.
- `/extensions/tabular_exports/*`: Summary tables embedded directly into the HDF5 as columnar exports, ensuring the artifact is self-contained.

---

## Metadata Examples

To provide context and provenance for the outputs in the HDF5 files, the pipeline generates several JSON metadata files. You can find reference examples in the `metadata_examples/` directory:

- **[`metadata_examples/run_manifest.json`](metadata_examples/run_manifest.json)**  
  This file includes the self-describing field guide for the HDF5 paths, describing the meaning of the exported data. It also records run parameters, enabled capabilities, dataset information, and global provenance hashes across the run.

- **[`metadata_examples/features_snapshot.json`](metadata_examples/features_snapshot.json)**  
  Shows the feature-layer side of the contract. It details explicit columns for grouped features, entropy provenance fields, and which sources were used as proxies (e.g., `embodied_arousal_proxy_source`), providing full auditability over how the HDF5 geometries were constructed.

- **[`metadata_examples/sub-02_task-imagery_run-1_bold.nii_qc_artifacts.json`](metadata_examples/sub-02_task-imagery_run-1_bold.nii_qc_artifacts.json)**  
  An example of per-file Quality Control (QC) artifacts. It documents preprocessing safeguards, epoch retention, resampling policies, and any identified missing support or dropped channels for that specific run, which ultimately dictate the validity of the Jacobian estimators within the corresponding `.h5` file.
