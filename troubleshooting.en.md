# ⚠️ Troubleshooting

> **Visual Overview:** Check out our [Architecture Flowchart](https://kwonnayeon.github.io/used-car-model-classification/assets/project_architecture.html) to understand where these issues occurred in the pipeline.

## 1. **Limitations of Roboflow Auto-labeling**
- **Issue:** Roboflow’s auto-labeling (AI-assisted bounding box generation) requires **paid tokens**, making it unavailable in free-tier environments.
- **Actions Taken:**
  - With ~33,000 images, manual labeling was not a viable option.
  - Initially planned a **semi-supervised learning approach**: label a small subset manually, then scale using auto-labeling to improve overall label quality.
  - However, once it was confirmed that Roboflow’s auto-labeling required paid credits, this strategy was discontinued.
- **Outcome:**
  - The object detection pipeline (YOLO) was dropped.
  - We pivoted to an image classification approach using **EfficientNet** and **ResNet**, which do not require bounding box annotations.

## 2. **Colab Resource and Time Constraints**
- **Issue:** Training on the full dataset was impractical in Colab (even with Pro) due to memory limits and session timeouts.
- **Actions Taken:**
  - Initially attempted training **EfficientNetB7** on the full dataset, but each epoch took over 4 hours.
  - Added model checkpointing and best-model saving logic, but session timeouts made these efforts ineffective.
- **Outcome:**
  - Considering time and compute constraints, we downsized the dataset.
  - Randomly sampled **10,000 images** from the full set to create a more manageable training pipeline.

## 3. **Unstable GPU Sessions and Long Training Times**
- **Issue:** GPU sessions in Colab frequently disconnected during long training runs, disrupting model convergence.
- **Actions Taken:**
  - Upgraded to **Colab Pro** for better GPU access.
  - Trained **EfficientNetB7** and **ResNet50**, but training remained slow (2–3 hours per epoch).
  - To address this:
    - Reduced **EfficientNet epochs from 50 to 20**
    - Reduced **ResNet epochs from 25 to 5**
- **Outcome:**
  - Integrated **checkpointing and model saving logic**, but Colab session limits still limited recovery.
  - Switched to **EfficientNetB0**, a lightweight alternative, for faster training.
  - Used reduced data and training cycles to adapt to resource constraints, acknowledging potential convergence and performance trade-offs.

## 4. **Class Omission Due to Subsampling**
- **Issue:** Reducing the dataset to 10,000 images (from 33,137 images across 396 classes) resulted in **missing class predictions** during evaluation.
- **Sampling Details:**
  - Performed **random sampling** with a target of at least **20 images per class**.
  - Conducted **noise image removal** for quality control prior to training.
- **Root Cause:**
  - Random sampling included exactly **20 images per class**.
  - However, the model produced **no outputs for certain classes**, likely due to **insufficient feature learning** or **suppression during inference**.
- **Outcome:**
  - Since some classes were missing from model predictions, zeroes were inserted for those classes to comply with the required submission format.

---

# 🔧 Technical Improvements

## Environment and Resource Optimization
- Move to **Colab Pro+**, **AWS**, or **GCP** for more stable long-term training environments.
- Set up environments with **high-performance GPUs** (e.g., **A100**, **V100**) to support longer training and larger models.

## Advanced Data Utilization
- Use **data augmentation** to increase pattern diversity and improve generalization with limited samples.
- Improve sampling strategy or leverage **distributed training (Multi-GPU, TPU)** to utilize the full dataset.

---

# 📈 Modeling Strategy Enhancements

## Lightweight Model Optimization
- Expand beyond EfficientNetB0 to explore **MobileNet** and other efficient architectures.
- Prioritize models with high **performance-to-compute efficiency** for fast iteration.

## Ensemble and Hybrid Approaches
- Apply **ensemble techniques** combining predictions from different architectures (e.g., ResNet, EfficientNet) to boost accuracy.
- Keep the door open for **hybrid pipelines** combining object detection and classification, if computational resources permit.

## Integration of Modern Architectures
- Explore **Vision Transformers (ViT)** and **ConvNeXt** using pretrained weights.
- For high-performance scenarios, test larger models like **ConvNeXt-Large** when resources allow.
- Maintain a dual-track strategy:  
  - Use lightweight models in constrained environments  
  - Reserve heavyweight models for accuracy-focused experiments
