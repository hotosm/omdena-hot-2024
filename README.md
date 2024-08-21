# Enhancing Global Mapping: YOLOv8 for the Segmentation of Rooftops in Aerial Imagery

This project focuses on enhancing global mapping capabilities with Ultralytic's YOLOv8 (You Only Look Once) to segment building rooftops from aerial images.

## Table of Contents

1. [Preprocessing](#preprocessing)
2. [Model Training](#model-training)
3. [Evaluation](#evaluation)
4. [Recommendations](#recommendations)
5. [Usage](#usage)

## Preprocessing

We downloaded the <a href="https://beta.source.coop/ramp/ramp/" target="_blank">Replicable AI for Microplanning (RAMP)</a> dataset from Source Cooperative. This dataset consists of 100,015 image-label pairs in TIF-GEOJSON formats, sourced from 22 different regions, and resulted from a collaborative initiative between DevGlobal and the World Health Organization. 

#### <a href="https://github.com/OmdenaAI/HOTOSM/blob/main/notebooks/preprocessing.ipynb" target="_blank">Preprocessing.ipynb</a>
The preprocessing notebook processes the initial data to create the V1 dataset. 
- Reduces the number of background images from 18% to 5%
- Removes errors discovered in the dataset after a limited manual review.
- Converts images from TIF-GEOJSON to YOLO-compatible JPG/TXT formats

#### <a href="https://github.com/OmdenaAI/HOTOSM/blob/main/notebooks/pruning.ipynb" target="_blank">Pruning.ipynb</a>
The pruning notebook enhances the quality of the dataset. 
- Creates the V2 dataset by removing problematic image-label pairs identified using Model 1's inference scores. 
- Generates the V3 dataset by excluding images from V2 with resolutions outside the 0.3 to 0.4 meters per pixel range

## Model Training

#### <a href="https://github.com/OmdenaAI/HOTOSM/blob/main/notebooks/training.ipynb" target="_blank">Training.ipynb</a>

The training notebook focuses on training the YOLOv8 segmentation model to segment rooftops using our custom data. Given the project's time and resource constraints, we selected the small YOLO model. However, future projects that can afford more computational resources and time should consider the YOLO's medium, large, and extra-large configurations as they tend to achieve higher performance. For efficient training, we recommend using a GPU, such as Google Colab's High-RAM T4 GPU, which can complete 100 epochs in approximately 15 hours. For efficient training, we recommend using a GPU, such as Google Colab's High-RAM T4 GPU, which can complete 100 epochs in approximately 15 hours.

Performance tuning found that the below training arguments improve model outcomes, which we documented in the <a href="https://github.com/OmdenaAI/HOTOSM/blob/main/data/args_tuned.yaml" target="_blank">args.yaml</a> file.
- Brightness Modification (hsv_v)
- Rotation (degrees)
- Left-right Flip Probability (fliplr)
- Classification Loss Weight (cls)

## Evaluation

Our evaluations utilized two test datasets to ensure a robust assessment.
1. **RAMP Test Set**: Comprising 11,902 images across 20 geographical regions, the models achieved varied F1 scores, with Model 2 scoring the highest at 72.3%.
2. **AI Innovation Dataset**: Consisting of 3,314 images across nine regions and three zoom levels, Model 2 achieved a top score of 45% before fine-tuning, improving to 92% after fine-tuning for Zoom Level 19 images.

## Recommendations

**Fine-Tuning**: YOLO models fine-tuned for specific geographical areas significantly outperform base models or models trained from scratch. This localized fine-tuning adapts the model to regional differences, such as building types and terrain, enhancing accuracy.

**Optimal Chip Size and Zoom Level**:
  - YOLO models perform best with 256x256 chip sizes at Zoom Level 19.
  - At higher Zoom Levels 20 and 21 with chip sizes of 256x256, performance decreases due to loss of contextual information.
  - We recommend merging four 256x256 chips into a 512x512 image or using YOLO’s native 640x640 image size. This strategy enhances edge details and provides more context, leading to better and faster model performance.

## Usage

To set up the project locally:

```bash
# Clone the repository
git clone https://github.com/OmdenaAI/HOTOSM.git

# Navigate to the project directory
cd HOTOSM

# Install the required packages
pip install -r requirements.txt
