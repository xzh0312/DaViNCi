---
layout: default
---
# DaViNCi

## Authors
Zihao Xie¹, Pingrui Lai¹, Yitong Wu¹, Hua Yang¹⁎.

xzh031202@sjtu.edu.cn, laipingrui@sjtu.edu.cn, maca8ka@sjtu.edu.cn, hyang@sjtu.edu.cn

¹Shanghai Jiao Tong University, Shanghai, China

⁎Corresponding author. 

Note: Both Zihao Xie and Pingrui Lai contributed equally to this research.

## Overviews
- We propose the DaViNCi (**D**yn**a**mic **Vi**sion-and-Language **N**avigation in **C**ont**i**nuous Environment) dataset, which achieves a paradigm shift from discrete to continuous and from static to dynamic representations in outdoor VLN datasets for vehicle.

- We propose test benchmarks of our dataset for both discrete and continuous environments.

- Through a series of experiments, we demonstrate both the validity and challenge of the dataset and provide a baseline in continuous environment.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/intro.png?raw=true)

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/datasets_table.png?raw=true)

## Construction Method of DaViNCi

**Simulator** : CARLA has been developed from the ground up to support development, training, and validation of autonomous driving systems. In addition to open-source code and protocols, CARLA provides open digital assets (urban layouts, buildings, vehicles) that were created for this purpose and can be used freely. The simulation platform supports flexible specification of sensor suites, environmental conditions, full control of all static and dynamic actors, maps generation and much more. [Link to Carla](https://carla.org/)

### Scenario Selection--Maps

**Town01** : A rural environment. The elements in the view are simple, with low-rise buildings and simple road structures. There are some characteristic elements such as streams and bridges.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/town01.png?raw=true)

**Town02** : Another rural environment. Compared to town01, the road structure is more compact and complex, with a relatively greater number of environmental elements.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/town02.png?raw=true)

**Town03** : A town environment involving elevation differences in roads, featuring wider roads and a more complex road structure. It includes some special elements such as roundabouts, uphill and downhill slopes, tunnels, five-way intersections, etc.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/town03.png?raw=true)

**Town04** : A highway environment including a small town area. There are classic highway elements such as multi-lane driving and entrance and exit ramps. In addition, the highway also involves a section of mountain road.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/town04.png?raw=true)

**Town05** : An urban environment. It encompasses towering buildings and intricate road structures. Also, it involves the construction of overpasses, incorporating vertical coordinates.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/town05.png?raw=true)

**Town10** : A prosperous urban environment. It encompasses a rich array of elements, including museums, cultural walls, large shopping malls, subway stations, parks, etc. It serves as the primary collection site for the dataset.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/town10.png?raw=true)

### Scenario Selection--Traffic Scenarios

**Traffic and other entities** : The agent needs to navigate various road traffic rules and interact appropriately with other vehicles, pedestrians, cyclists, etc.

**Traffic intersections** : The agent needs to comprehensively interact with various information in the intersection road structure.

**Roundabout** : The agent needs to comprehensively interact with various information in the roundabout road structure.

**Lane Change** : The agent needs to perform lane-changing operations at the right time to reasonably navigate the prescribed route at key intersections.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/TrafficScenarios.png?raw=true)

### Path Generation
In simulation environments configured to exclude all additional dynamic traffic participants and moving obstacles, a randomly spawned autonomous vehicle is initialized to execute free navigation tasks in the virtual simulated scenario. During the entire navigation process, the vehicle’s behavioral decisions and operational maneuvering strategies at key spatial nodes and critical positional waypoints are autonomously determined and executed by the autonomous driving control program in real time. Meanwhile, positional coordinate data of the ego vehicle are continuously sampled and recorded at a fixed sampling interval of one second; subsequently, the sequentially acquired position waypoints are connected and fitted in chronological order, thereby constructing and generating the accurate ground-truth reference path corresponding to each independent navigation traversal trial. The trajectory data is stored in a JSON file, containing road information and position coordinate information for each frame.
```

{
        "time_sec": 1.0, "road_id": 22, "lane_id": 1, "section_id": 0, "lane_change": 1, "is_junction": false,
        "s": 19.079432093556104, "x": -45.28676223754883, "y": -24.248592376708984, "z": 2.0,
        "yaw": 270.43231201171875, "pitch": 360.0, "roll": 0.0
},
...
```
<div style="text-align: center;">
  <video 
    src="https://github.com/xzh0312/DaViNCi/blob/master/imgs/PathGeneration.mp4?raw=true" 
    controls 
    width="80%" 
    style="max-width: 100%;"
  >
  </video>
</div>

### Instruction Generation
During the vehicle operation process described above, first-person driving videos are recorded to provide source material for instruction generation. To address the limitations of existing VLMs in generating navigation descriptions from first-person driving videos, this paper proposes a two-stage processing framework and then perform manual fine-tuning.

#### Step 1 Quantification of Turning Information Based on Trajectory Data
This step extracts precise turning directions and temporal intervals from trajectory JSON files via mathematical fitting and logical inference, providing reliable prior knowledge for subsequent video analysis and eliminating visual judgment biases.

Data Preparation & Path Processing: Taking the multi-level root directory of trajectory JSON data (containing coordinates, timestamps, road IDs and other attributes) as input, the output preserves the original folder hierarchy and generates a dedicated directory to store turning analysis results.
Trajectory Parsing & Preprocessing: Core information is extracted from JSON files; X/Y coordinates are axis-mirrored to fit standard visual coordinate systems, while timestamps and road IDs of each trajectory point are retained.

Segmented Trajectory Fitting: Continuous trajectories are divided into horizontal/vertical straight-driving segments by statistical thresholding: segments with low x-coordinate standard deviation are defined as longitudinal driving, and those meeting y-coordinate deviation constraints as lateral driving. Each segment is annotated with driving orientation, affiliated road ID and start-end indices.

Segment Merging & Turning Detection: Homogeneous consecutive segments are merged to reduce redundancy; vector cross-product calculation identifies left/right turns at heterogeneous segment junctions, and timestamps are calibrated with a -1 second offset to correct systematic errors.

Result Export: Turning events (timestamp range and direction) are recorded in result.txt; a visualized trajectory diagram with raw paths, fitted segments and time annotations is generated for verification.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/instruction.png?raw=true)

#### Step 2 Natural Language Caption Generation for Videos with Calibrated Turning Facts
This step adopts the Tongyi Qwen 3-VL-Plus multimodal API. The precise turning information derived from Step 1 is integrated into prompts to guide video content analysis and generate logically consistent navigation descriptions.

Path Matching & Data Loading: The video directory with a consistent structure to trajectory files is recursively traversed to match each MP4 video with its corresponding Step 1 result file. Turning directions and temporal intervals are loaded as factual prompt constraints.

Multimodal API Invocation:
Customized high-precision prompts are constructed with the following constraints.

```
1. The turning directions defined in Step 1 must be strictly followed; timestamps are only used as internal references and prohibited from appearing in final outputs;
2. All outputs adopt standardized navigation instruction formats (e.g., Go straight → Turn right → Drive along the left curve) without any temporal information;
3. Real-world landmarks are extracted from video frames (e.g., Turn right at the traffic light, Drive along the left curve beside the supermarket);
4. Clear distinctions are made between intersection turns and road curves to accurately describe the trigger points of vehicle maneuvers.
```

The MultiModalConversation interface of Tongyi Qwen is called, with local video files and customized prompts as inputs, to acquire natural language caption outputs.

### Dynamic Environment
<div style="text-align: center;">
  <video 
    src="https://github.com/xzh0312/DaViNCi/blob/master/imgs/Dynamic.mp4?raw=true" 
    controls 
    width="80%" 
    style="max-width: 100%;"
  >
  </video>
</div>

## Experiments
### Discrete Environment
**Discretization Adaptation**. The partial extracts of four discretized maps are as follows. The partial extracts of the four discretized maps are shown below. Town01, Town02, and Town10 adopt a fine granularity of 5m, while Town03 adopts a coarse granularity of 12m. Red nodes represent nodes with only one exit, while yellow nodes represent nodes with multiple exits.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/Dis.png?raw=true)

**Recent Baseline**.

*VELMA*: This work proposes a specialized LLM-based agent that leverages linguistic representations of trajectories and visual environmental observations as contextual prompts for subsequent actions. The visual information is processed through a pipeline that extracts landmarks from human-written navigation instructions and employs CLIP to determine their visibility within the current panoramic view.

*FLAME*: This method introduces a three-stage adaptive algorithm, consisting of (1) a single-perception adaptation for streetview descriptions, (2) a multi-perception adaptation for path summarization, and (3) an end-to-end training algorithm on VLNdatasets. The resulting framework yields a novel multimodal LLM-based agent and architecture.

*Mem4Nav*: This approach proposes a hierarchical spatial cognitive long-short-term memory system that can enhance any VLN backbone model. It integrates (1) a sparse octree for ffnegrained voxel indexing and (2) a semantic topological graph for highlevel landmark association, both stored in trainable memory tokens via invertible Transformer embeddings. The long-term memory (LTM) compresses and retains historical observations at octree and graph nodes, while the short-term memory (STM) caches recent multimodal entries in relative coordinates for real-time obstacle avoidance and local planning.

**Results**
![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/DisResults.png?raw=true)

**Typical Instance**. The memory and alignment pressure brought by long-distance navigation manifest as errors in the later stages of navigation.
![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/DisExample.png?raw=true)

### Continuous Environment

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/COVL-RL.png?raw=true)
**COVL-RL**. At each time step, the model receives textual navigation instructions, current visual observations, and historical context to predict the agent's immediate action:

\[
a_{t} \sim \pi_{\theta}\left(a_{t} \mid \mathcal{I}, \mathcal{H}_{t}, \mathcal{V}_{t}\right)
\]

\(\mathcal{I}\) denotes the textual instruction, \(\mathcal{V}_{t}\) represents the visual observation at timestep \(t\), and \(\mathcal{H}_{t}\) encapsulates the historical trajectory up to but excluding timestep \(t\). The historical context \(\mathcal{H}_{t}\) comprises both past visual observations and the corresponding executed actions, such that:

\[
\mathcal{H}_{t} = \{\mathcal{V}_{1}, A_{1}, \mathcal{V}_{2}, A_{2}, \ldots, \mathcal{V}_{t-1}, A_{t-1}\}
\]

The RL framework continuously optimizes the action policy through reward shaping, where actions aligning with the ground-truth trajectory receive positive rewards while deviating actions incur penalties. The optimization objective is formulated as:

\[
\begin{aligned}
\mathcal{L}^{\text{PPO}} &= \mathcal{L}^{\text{Actor}} + \mathcal{L}^{\text{Critic}} - \beta \mathcal{H} \\
\mathcal{L}^{\text{Actor}} &= \mathbb{E}_t \left[ \min \left( r_t(\theta) A_t, \text{clip}\left(r_t(\theta), 1-\epsilon, 1+\epsilon\right) A_t \right) \right] \\
\mathcal{L}^{\text{Critic}} &= \frac{1}{2} \mathbb{E}_t \left[ (V_\theta(s_t) - R_t)^2 \right] \\
\mathcal{H} &= \mathbb{E}_t \left[ \text{Entropy}\left( \pi_\theta(\cdot|s_t) \right) \right]
\end{aligned}
\]

The PPO objective \(\mathcal{L}^{\text{PPO}} = \mathcal{L}^{\text{Actor}} + \mathcal{L}^{\text{Critic}} - \beta\mathcal{H}\) optimizes policy \(\pi_\theta\) through: (1) \(\mathcal{L}^{\text{Actor}}\) using clipped ratio \(r_t(\theta) = \pi_\theta/\pi_{\theta_{\text{old}}}\) and advantage \(A_t\), (2) \(\mathcal{L}^{\text{Critic}}\) minimizing TD error \((V_\theta(s_t) - R_t)^2\), and (3) entropy \(\mathcal{H}\) for exploration. Hyperparameters \(\epsilon\) clips \(r_t(\theta)\) in \([1-\epsilon,1+\epsilon]\), while \(\beta\) weights \(\mathcal{H}\). The expectations \(\mathbb{E}_t\) are over trajectories.

The reward function is designed as follows:

\[
\mathcal{R} = 5 + \mathcal{R}_{\text{dist}} + \mathcal{R}_{\text{dest}}
\]

The distance reward \(\mathcal{R}_{\text{dist}}\) quantifies the agent's deviation from the ground-truth trajectory. At each timestep \(t\), the navigation system identifies all ground-truth waypoints \(\mathcal{wp}_t \in \{wp_1, \ldots, wp_k\}\) in a 10-meter lookahead distance from the agent's current position. When action \(a_t\) is executed and the agent transitions to state \(s_{t+1}\), the reward is computed as the negative minimum Euclidean distance between the new position and the previously identified waypoints:

\[
\mathcal{R}_{\text{dist}} = -\text{distance}\left(\text{position}_{t}, \text{wp}_{t}\right)
\]

The destination reward \(\mathcal{R}_{\text{dest}}\) provides terminal feedback based on the agent's final stopping position \(s_T\) relative to the target destination \(D\):

\[
\mathcal{R}_{\text{dest}} =
\begin{cases}
100 - \text{distance}\left(s_T, D\right), & \text{in range} \\
-100 - \text{distance}\left(s_T, D\right), & \text{out of range}
\end{cases}
\]

Furthermore, the study revealed that during the training process, should the agent select an incorrect action at a critical timestep, it would subsequently engage in prolonged and futile exploration along an erroneous trajectory. During this period, the system would consistently provide a significantly negative reward value. This phenomenon not only results in substantial wastage of training time but also leads the model to erroneously penalize all subsequent actions, despite the initial mistake being confined to that single critical step. To address this issue, we have designed an early termination strategy that immediately halts the current training episode when the agent significantly deviates from the ground-truth trajectory:

\[
\mathcal{R} < -10
\]

**Results**
![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/DaViNCiResults.png?raw=true)

**Impact of Granularity**
![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/granularities.png?raw=true)

**Impact of Dynamic Elements**
![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/DynamicNumber.png?raw=true)

**Typical Instance**
