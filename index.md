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






Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](./another-page.html).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png)

### Large image

![Branching](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
