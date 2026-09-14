# Efficient-CKD-TransBTS
Efficient CKD-TransBTS for 3D glioma segmentation through architecture adaptation and Taguchi-based hyperparameter optimization.

Efficient CKD-TransBTS for 3D Glioma Segmentation

An efficient CKD-TransBTS framework for 3D glioma segmentation through architecture adaptation and Taguchi-based hyperparameter optimization.

Overview

This project presents an efficient adaptation of CKD-TransBTS for 3D glioma segmentation using multi-modal MRI data from the BraTS-GLI 2024 dataset.

The original CKD-TransBTS architecture provides strong segmentation performance but has relatively high computational complexity. This project focuses on reducing the model complexity while maintaining competitive segmentation performance.

The approach consists of two main stages:

Architecture adaptation to reduce the model's computational and memory requirements.
Taguchi-based hyperparameter optimization to determine an efficient training configuration.

The final model is implemented and trained using PyTorch and MONAI, with the complete implementation provided in a Google Colab-compatible notebook.

Note: The Taguchi method was used to select the final hyperparameter configuration. The notebook included in this repository contains the implementation and final training of the selected L5 configuration, rather than the complete L1–L9 Taguchi experiment.

Key Contributions
Adapted the CKD-TransBTS architecture to significantly reduce model complexity.
Reduced the number of parameters from approximately 82.28M to 1.04M.
Applied Taguchi-based hyperparameter optimization using an L9 orthogonal array.
Identified L5 as the selected configuration:
Learning rate: 0.0001
Optimizer: AdamW
Patch size: 64 × 64 × 64
Evaluated segmentation performance using Dice Score, Sensitivity, and HD95.
Evaluated inference efficiency against the reference implementation.
Model Architecture

The adapted model retains the core multi-modal feature learning concept of CKD-TransBTS while simplifying its architecture to reduce computational requirements.

Architecture Adaptation

The main adaptations include:

Base channel reduced to 16.
Constant base channel configuration across stages.
Convolutional bottleneck replacing the original transformer-based bottleneck.
Three sequential Modality-Correlated Cross-Attention (MCCA) blocks.
MCCA base channel set to 32.
Attention heads fixed at 2.
Multi-modal MRI inputs are grouped as:
T1c + T1n
T2f + T2w
Feature calibration is performed using the Transformer & CNN Feature Calibration (TCFC) component.

These adaptations reduce the computational requirements while preserving the main feature extraction and multi-modal fusion mechanisms.

Dataset

The model was evaluated using the BraTS-GLI 2024 dataset.

The dataset contains four MRI modalities:

T1n
T1c
T2w
T2f

The segmentation labels represent four tumor subregions:

NETC — Non-enhancing tumor core
SNFH — Surrounding non-enhancing FLAIR hyperintensity
ET — Enhancing tumor
RC — Resection cavity

Compound tumor regions were also evaluated:

TC = ET + NETC
WT = ET + NETC + SNFH

The dataset is not included in this repository due to its size and licensing/distribution conditions. Please obtain the dataset from the official BraTS/CBICA distribution source before running the notebook.

Preprocessing

The preprocessing pipeline includes:

MRI loading
Channel-first conversion
RAS orientation
Resampling to 1 × 1 × 1 mm
Label validation
Intensity normalization using the 1st–99th percentile range
Foreground cropping
Spatial augmentation
Intensity augmentation
Padding
Patch-based training with 64 × 64 × 64 patches

Data augmentation includes random:

Flipping
90-degree rotation
Affine transformation
3D elastic deformation
Zoom
Gaussian noise
Taguchi Hyperparameter Optimization

Taguchi's method was used to evaluate three training factors at three levels using an L9 orthogonal array.

Factor	Level 1	Level 2	Level 3
Learning Rate	0.001	0.0001	0.00001
Optimizer	Adam	AdamW	SGD
Patch Size	128³	96³	64³

The L5 configuration produced the highest Mean Dice among the tested configurations:

Configuration	Learning Rate	Optimizer	Patch Size	Mean Dice
L5	0.0001	AdamW	64³	0.6553

The selected L5 configuration was subsequently used for final model training.

Experimental Setup

The final model was trained using:

Optimizer: AdamW
Learning rate: 1e-4
Weight decay: 1e-5
Epochs: 100
Learning rate scheduler: CosineAnnealingLR
Minimum learning rate: 1e-5
Automatic Mixed Precision (AMP)
Gradient accumulation
Gradient clipping
Combined Cross-Entropy and Generalized Dice Loss

The final dataset was divided into:

Training: 1,081 cases
Validation: 270 cases
Testing: 270 cases

The split was performed using a fixed random seed of 42.

Results

Limitations

Several limitations should be considered:

The final training was limited to 100 epochs, compared with 261 epochs reported for the reference experiment, due to computational limitations.
The final model was trained and evaluated in a resource-constrained environment.
Dice performance for ET and RC was lower than for larger tumor regions such as WT and SNFH.
Patch sampling primarily targeted the general tumor region and did not specifically prioritize smaller ET and RC regions.
The Taguchi analysis in this study was used primarily for configuration selection and did not include a formal S/N ratio and ANOVA analysis.
Citation

If you use this implementation or research in your work, please cite the corresponding thesis/research publication.

@thesis{rifandy2026ckdtransbts,
  author  = {Zul Tiandra Rifandy},
  title   = {Optimisasi Hyperparameter Model CKD-TransBTS Menggunakan Metode Taguchi untuk Segmentasi Glioma Otak 3D secara Efisien},
  school  = {Universitas Esa Unggul},
  year    = {2026}
}
License

This project is intended for academic and research purposes.

See the LICENSE file for details.

The adapted model achieved competitive segmentation performance while substantially reducing model complexity.

Metric	Optimized CKD-TransBTS	Reference
Parameters	1.04M	82.28M
Mean Dice	0.6989	0.7151
Sensitivity	0.8205	0.8058
HD95	5.77 mm	6.32 mm
Average Inference Time	86.18 s	144.13 s
Key Findings
Approximately 79× fewer parameters than the reference model.
Approximately 1.67× faster average inference.
Mean Dice remained close to the reference result despite the substantial reduction in model complexity.
Sensitivity and HD95 were also competitive with the reference results.
Dice Score by Region
Region	Dice
ET	0.5846
TC	0.5849
WT	0.8598
NETC	0.7117
SNFH	0.8443
RC	0.6080
Inference Performance

Inference performance was evaluated by comparing the optimized model with the reference implementation.

Case	Optimized	Reference
Anton	86.09 s	167.31 s
Rudi	100.80 s	107.10 s
Siti	71.64 s	157.97 s
Average	86.18 s	144.13 s

The optimized model achieved an average inference time of 86.18 seconds, compared with 144.13 seconds for the reference model.
