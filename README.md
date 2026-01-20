<div style="font-family: 'Times New Roman', Times, serif; font-size: 16px; color: #333;">

<h1 align="center">LLM-Driver: A Vision–Language–Enhanced Multimodal Reasoning Framework for Vehicle Simulation</h1>

<p>
This repository hosts the implementation and evaluation of <strong>LLM-Driver</strong>, a closed-loop autonomous driving framework that integrates <strong>multimodal large language models (MLLMs)</strong> into the CARLA simulator. It addresses the fundamental challenge of pedestrian intent prediction and safety-critical control in urban environments by bridging the gap between geometric perception and semantic reasoning.
</p>

<hr>

<h2>🧠 Framework Overview</h2>

<p>
The <strong>LLM-Driver framework</strong> shifts autonomous vehicle decision-making from purely physics-based prediction to semantic reasoning in social space. The system features:
</p>

<ul>
    <li><strong>Multimodal Reasoning Layer:</strong> Combines <strong>Vision-Language Models (Qwen3-VL)</strong> for visual scene description with <strong>Reasoning Models (GPT-o1)</strong> for intent inference.</li>
    <li><strong>Few-Shot Learning:</strong> Leverages real-world behavioral exemplars (extracted from the PIE dataset) to ground reasoning in authentic human interactions.</li>
    <li><strong>Demographic-Adaptive Control:</strong> A tiered safety controller that adjusts braking margins based on pedestrian age groups (Child, Adult, Senior) to ensure equitable safety protection.</li>
</ul>

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/43e0c10b93c5e2bc4d408a2b0e679c96b5707dce/Config/1.png" alt="Framework Architecture" width="800">
  <br>
  <em>Figure 1: The LLM-Driver framework architecture operating in closed-loop mode within CARLA.</em>
</p>

<hr>

<h2>⚙️ Methodology</h2>

<p>The system operates via an asynchronous two-level architecture:</p>

<h3>1. Upper Level: Multimodal Perception and Reasoning</h3>
<p>This module infers latent pedestrian crossing intentions (Yielding vs. Non-Yielding) using a chain-of-thought process.</p>

<ul>
    <li><strong>Multimodal Inputs:</strong>
        <ul>
            <li><strong>Vision:</strong> Front-view camera image processed by Qwen3-VL 8B to generate structured scene descriptions.</li>
            <li><strong>Kinematics:</strong> Historical trajectory logs (JSON format) containing vehicle and pedestrian states.</li>
        </ul>
    </li>
    <li><strong>Reasoning Core:</strong>
        <ul>
            <li>The reasoning model (GPT-o1 20B) receives the scene description, kinematic logs, and <strong>few-shot real-world exemplars</strong>.</li>
            <li>It outputs a structured analysis including Visual Analysis, Kinematic Analysis, Decision, and Reasoning.</li>
        </ul>
    </li>
</ul>

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/bf7b7684366c005f08504d559b04b7814703a0ed/Config/2.1.png" alt="Real-world Exemplar" width="800">
  <br>
  <em>Figure 2: A real-world few-shot exemplar used in the prompt, integrating visual context, kinematic logs, and structured reasoning.</em>
</p>

<h3>2. Lower Level: Demographic-Adaptive Tiered Safety Control</h3>
<p>Translates high-level intent into precise control commands.</p>

<ul>
    <li><strong>Control Logic:</strong>
        <ul>
            <li><strong>Yielding:</strong> Vehicle maintains Autopilot mode.</li>
            <li><strong>Non-Yielding:</strong> Vehicle switches to Emergency mode.</li>
        </ul>
    </li>
    <li><strong>Demographic Adaptation (<span style="font-family: 'Times New Roman', serif;">α<sub>demo</sub></span>):</strong> Safety margins are scaled based on detected demographic groups to account for varying risk profiles.
        <ul>
            <li><strong>Child (<span style="font-family: 'Times New Roman', serif;">α = 1.4</span>):</strong> High behavioral uncertainty.</li>
            <li><strong>Senior (<span style="font-family: 'Times New Roman', serif;">α = 1.2</span>):</strong> Reduced mobility.</li>
            <li><strong>Adult (<span style="font-family: 'Times New Roman', serif;">α = 1.0</span>):</strong> Baseline behavior.</li>
        </ul>
    </li>
    <li><strong>Braking Tiers:</strong> Implements graduated deceleration (0.2g, 0.4g, 0.7g, 1.0g) based on collision imminence.</li>
</ul>

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/bf7b7684366c005f08504d559b04b7814703a0ed/Config/2.png" alt="Control Architecture" width="800">
  <br>
  <em>Figure 3: Demographic-Adaptive Tiered Safety Control Architecture.</em>
</p>

<hr>

<h2>📊 Results</h2>

<p>Experiments were conducted in <strong>CARLA Town10HD</strong> using 112 intent classification scenarios and 200 safety evaluation scenarios.</p>

<h3>Classification Performance (Ablation Study)</h3>
<p>The integration of real-world few-shot priors provides increasing marginal benefits as scenario complexity rises.</p>

<table border="1" cellpadding="5" cellspacing="0" style="border-collapse: collapse; width: 100%; font-family: 'Times New Roman', Times, serif;">
    <thead>
        <tr style="background-color: #f2f2f2;">
            <th align="left">Method</th>
            <th align="left">Accuracy</th>
            <th align="left">Recall (Non-Yield)</th>
            <th align="left">False Negative Rate</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Rule-based Baseline</td>
            <td>78.4%</td>
            <td>81.3%</td>
            <td>18.7%</td>
        </tr>
        <tr>
            <td>Vision-only LLM</td>
            <td>82.8%</td>
            <td>84.0%</td>
            <td>16.0%</td>
        </tr>
        <tr>
            <td>Kinematics-only LLM</td>
            <td>83.9%</td>
            <td>85.3%</td>
            <td>14.7%</td>
        </tr>
        <tr>
            <td>Zero-shot LLM</td>
            <td>88.4%</td>
            <td>89.8%</td>
            <td>10.2%</td>
        </tr>
        <tr>
            <td><strong>Real-world Few-shot LLM</strong></td>
            <td><strong>92.3%</strong></td>
            <td><strong>94.1%</strong></td>
            <td><strong>5.9%</strong></td>
        </tr>
    </tbody>
</table>

<h3>Safety Analysis</h3>
<p>The LLM-Driver significantly shifts the Time-to-Collision (TTC) distribution toward safer values.</p>
<ul>
    <li><strong>Conflict Reduction:</strong> Reduces extreme-risk interactions (TTC < 2.0s) by <strong>73%</strong> compared to the rule-based baseline.</li>
    <li><strong>Traffic Efficiency:</strong> Achieves a <strong>62% reduction</strong> in unnecessary braking events in yielding scenarios.</li>
</ul>

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/653959e3e9e375f36c913e4258b51af45c8d2cfd/Config/44.png" alt="TTC Distribution" width="800">
  <br>
  <em>Figure 4: Distribution of minimum TTC values across system configurations.</em>
</p>

<h3>Control Dynamics</h3>
<p>Unlike reactive baselines where TTC continuously degrades, LLM-Driver stabilizes TTC within the trigger zone through anticipatory intent recognition.</p>

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/ba796376441ba06cd07ce6e870d3b8846cb48bd1/Config/555.png" alt="TTC Evolution" width="800">
  <br>
  <em>Figure 5: Temporal TTC evolution comparing (a) Rule-based baseline and (b) LLM-Driver with few-shot reasoning.</em>
</p>

<hr>

<h2>🔬 Qualitative Demonstration</h2>

<p>The system generates interpretable reasoning chains explaining <em>why</em> a classification was made. Below are representative cases showing how the model distinguishes intent across demographics.</p>

<p align="center">
  <img src="https://github.com/Qpu523/Drive_X/blob/45fe005a08ef83e02bf061ae0fac6b5f1a65cf77/Config/66.png" alt="Senior Non-Yielding Demonstration" width="800">
  <br>
  <em>Figure 6: Vision–language reasoning demonstration for a <b>Senior Non-Yielding</b> scenario. (Left) Multimodal inputs showing an elderly pedestrian; (Center) High-level intention inference predicting "Non-Yielding"; (Right) Control execution triggering emergency braking.</em>
</p>

<hr>

<h2>📌 Citation</h2>

<p>If you use this framework or methodology in your research, please cite the following paper:</p>

<pre style="font-family: 'Times New Roman', Times, serif; background-color: #f6f8fa; padding: 16px; border-radius: 6px;">
@article{
}
</pre>

</div>
