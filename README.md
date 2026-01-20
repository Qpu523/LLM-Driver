# LLM-Driver: A Vision–Language–Enhanced Multimodal Reasoning Framework for Vehicle Simulation

This repository hosts the implementation and evaluation of **LLM-Driver**, a closed-loop autonomous driving framework that integrates **multimodal large language models (MLLMs)** into the CARLA simulator. It addresses the fundamental challenge of pedestrian intent prediction and safety-critical control in urban environments by bridging the gap between geometric perception and semantic reasoning.

---

## 🧠 Framework Overview

The **LLM-Driver framework** shifts autonomous vehicle decision-making from purely physics-based prediction to semantic reasoning in social space. The system features:

-   **Multimodal Reasoning Layer:** Combines **Vision-Language Models (Qwen3-VL)** for visual scene description with **Reasoning Models (GPT-o1)** for intent inference.
-   **Few-Shot Learning:** Leverages real-world behavioral exemplars (extracted from the PIE dataset) to ground reasoning in authentic human interactions.
-   **Demographic-Adaptive Control:** A tiered safety controller that adjusts braking margins based on pedestrian age groups (Child, Adult, Senior) to ensure equitable safety protection.

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/43e0c10b93c5e2bc4d408a2b0e679c96b5707dce/Config/1.png" alt="Framework Architecture" width="800">
  <br>
  <em>Figure 1: The LLM-Driver framework architecture operating in closed-loop mode within CARLA.</em>
</p>

---

## ⚙️ Methodology

The system operates via an asynchronous two-level architecture:

### 1. Upper Level: Multimodal Perception and Reasoning
This module infers latent pedestrian crossing intentions (Yielding vs. Non-Yielding) using a chain-of-thought process.

* **Multimodal Inputs:**
    * **Vision:** Front-view camera image processed by Qwen3-VL 8B to generate structured scene descriptions.
    * **Kinematics:** Historical trajectory logs (JSON format) containing vehicle and pedestrian states.
* **Reasoning Core:**
    * The reasoning model (GPT-o1 20B) receives the scene description, kinematic logs, and **few-shot real-world exemplars**.
    * It outputs a structured analysis including Visual Analysis, Kinematic Analysis, Decision, and Reasoning.

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/bf7b7684366c005f08504d559b04b7814703a0ed/Config/2.1.png" alt="Real-world Exemplar" width="800">
  <br>
  <em>Figure 2: A real-world few-shot exemplar used in the prompt, integrating visual context, kinematic logs, and structured reasoning.</em>
</p>

### 2. Lower Level: Demographic-Adaptive Tiered Safety Control
Translates high-level intent into precise control commands.

* **Control Logic:**
    * **Yielding:** Vehicle maintains Autopilot mode.
    * **Non-Yielding:** Vehicle switches to Emergency mode.
* **Demographic Adaptation ($\alpha_{demo}$):** Safety margins are scaled based on detected demographic groups to account for varying risk profiles.
    * **Child ($\alpha = 1.4$):** High behavioral uncertainty.
    * **Senior ($\alpha = 1.2$):** Reduced mobility.
    * **Adult ($\alpha = 1.0$):** Baseline behavior.
* **Braking Tiers:** Implements graduated deceleration ($0.2g, 0.4g, 0.7g, 1.0g$) based on collision imminence.

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/bf7b7684366c005f08504d559b04b7814703a0ed/Config/2.png" alt="Control Architecture" width="800">
  <br>
  <em>Figure 3: Demographic-Adaptive Tiered Safety Control Architecture.</em>
</p>

---

## 📊 Results

Experiments were conducted in **CARLA Town10HD** using 112 intent classification scenarios and 200 safety evaluation scenarios.

### Classification Performance (Ablation Study)
The integration of real-world few-shot priors provides increasing marginal benefits as scenario complexity rises.

| Method | Accuracy | Recall (Non-Yield) | False Negative Rate |
| :--- | :--- | :--- | :--- |
| Rule-based Baseline | 78.4% | 81.3% | 18.7% |
| Vision-only LLM | 82.8% | 84.0% | 16.0% |
| Kinematics-only LLM | 83.9% | 85.3% | 14.7% |
| Zero-shot LLM | 88.4% | 89.8% | 10.2% |
| **Real-world Few-shot LLM** | **92.3%** | **94.1%** | **5.9%** |

### Safety Analysis
The LLM-Driver significantly shifts the Time-to-Collision (TTC) distribution toward safer values.
* **Conflict Reduction:** Reduces extreme-risk interactions (TTC < 2.0s) by **73%** compared to the rule-based baseline.
* **Traffic Efficiency:** Achieves a **62% reduction** in unnecessary braking events in yielding scenarios.

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/653959e3e9e375f36c913e4258b51af45c8d2cfd/Config/44.png" alt="TTC Distribution" width="800">
  <br>
  <em>Figure 4: Distribution of minimum TTC values across system configurations.</em>
</p>

### Control Dynamics
Unlike reactive baselines where TTC continuously degrades, LLM-Driver stabilizes TTC within the trigger zone through anticipatory intent recognition.

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/ba796376441ba06cd07ce6e870d3b8846cb48bd1/Config/555.png" alt="TTC Evolution" width="800">
  <br>
  <em>Figure 5: Temporal TTC evolution comparing (a) Rule-based baseline and (b) LLM-Driver with few-shot reasoning.</em>
</p>

---

## 🔬 Qualitative Demonstration

The system generates interpretable reasoning chains explaining *why* a classification was made. Below are representative cases showing how the model distinguishes intent across demographics.

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/45fe005a08ef83e02bf061ae0fac6b5f1a65cf77/Config/66.png" alt="Senior Non-Yielding Demonstration" width="800">
  <br>
  <em>Figure 6: Vision–language reasoning demonstration for a <b>Senior Non-Yielding</b> scenario. (Left) Multimodal inputs showing an elderly pedestrian; (Center) High-level intention inference predicting "Non-Yielding"; (Right) Control execution triggering emergency braking.</em>
</p>
---

## 📌 Citation

If you use this framework or methodology in your research, please cite the following paper:

```bibtex
@article{
}
