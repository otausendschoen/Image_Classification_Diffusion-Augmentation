# Master's Thesis: Synthetic Satellite Image Generation using Diffusion Models

**Barcelona School of Economics**  
**Master’s in Data Science Methodology**  
**Authors:** Gerardo Gómez Argüelles, Oliver Tausendschön, Timothy Cassel  
**Supervisor:** Antonio Lozano  
**Date:** June 2025  

---

## Research Question

**Can class-conditional Denoising Diffusion Probabilistic Models (DDPMs) generate synthetic satellite images that improve classification performance on small datasets like EuroSAT, compared to traditional data augmentation methods?**

---

##  Abstract

This thesis evaluates the effectiveness of synthetic satellite images generated via DDPMs for augmenting low-resource classification datasets. Using the EuroSAT dataset and a ResNet-18 classifier, we compare synthetic data augmentation to standard geometric transformations across multiple data sizes (100%, 50%, 10%).

Key finding: While geometric augmentations consistently improved performance, synthetic augmentation often reduced accuracy unless used sparingly or in hybrid combinations with traditional methods. Notably, hybrid augmentation improved performance for certain land cover classes like *Annual Crop*.

---

## Repository Structure and File Descriptions

###  `/Codes/`
Jupyter notebooks for training and evaluating models:

- `01_diffusion_model_conditional.ipynb`: Train DDPM on the full (100%) dataset  
- `01_diffusion_model_conditional_10.ipynb`: Train DDPM on 10% of EuroSAT  
- `01_diffusion_model_conditional_50.ipynb`: Train DDPM on 50% of EuroSAT  
- `02_Image_Generation.ipynb`: Run DDPM inference and generate synthetic images  
- `02_Classification.ipynb`: Train baseline + augmented classifiers  
- `03_ResNet18_Classifier.ipynb`: Core classification pipeline using ResNet-18  
- `04_Classifier_Tests.ipynb`: Experiments for comparing results across settings  
- `05_Significance.ipynb`: Statistical tests on classification accuracy (Welch’s t-tests)

### `/Codes/docker_file/`
Docker-ready deployment pipeline:

- `Dockerfile`: Full container spec for reproducibility (Diffusion + ResNet18)
- `requirements.txt`: Python dependencies for both training and inference
- `05_claudia10_metrics.py`: Evaluation utilities for classification results
- `data/`: Local mount for generated images (used by Docker)
- `README.md`: Guide to deploying the classifier on GPU droplets (e.g., DigitalOcean)

### `/data/Inputs/`
Raw datasets and generated synthetic images (zipped):

- `eurosat-dataset.zip`: Original EuroSAT RGB dataset  
- `ddpm-generated-images.zip`: Synthetic images from DDPM (full training)  
- `ddpm-generated-images_10.zip`: Generated from DDPM trained on 10% of data  
- `ddpm-generated-images_50.zip`: Generated from DDPM trained on 50% of data  


### `/data/Outputs/`
Results from all experimental runs:

- `accuracy_across_runs.csv`: Accuracy scores across all setups  
- `f1_across_runs.csv`: Weighted F1 scores  
- `confusion_across_runs.csv`: Confusion matrix summaries  
- `all_runs_results.csv`: Combined metrics per experiment  
- `all_runs_results_10.csv`: Subset analysis for 10% data  
- `all_runs_results_2.csv`: Internal testing (can be ignored or removed)

---

## Methodology

- Dataset: **EuroSAT RGB** (10 classes, 27,000 images, 64×64 px)
- Model: **Class-conditional DDPM** trained with UNet backbone
- Classifier: **ResNet-18**
- Augmentation types:
  - No augmentation (baseline)
  - Geometric (flip, rotate)
  - Synthetic (5–50% of training size)
  - Hybrid (geometric + synthetic)

---


## 🐳 Running with Docker (Optional)

To run training and inference on a GPU machine, please refer to the README in the docker folder
