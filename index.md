---
layout: default
---

# Overviews
- We propose the DaViNCi (**D**yn**a**mic **Vi**sion-and-Language **N**avigation in **C**ont**i**nuous Environment) dataset, which achieves a paradigm shift from discrete to continuous and from static to dynamic representations in outdoor VLN datasets for vehicle.

- We propose test benchmarks of our dataset for both discrete and continuous environments.

- Through a series of experiments, we demonstrate both the validity and challenge of the dataset and provide a baseline in continuous environment.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/intro.png?raw=true)

# Construction Method of DaViNCi

**Simulator** : CARLA has been developed from the ground up to support development, training, and validation of autonomous driving systems. In addition to open-source code and protocols, CARLA provides open digital assets (urban layouts, buildings, vehicles) that were created for this purpose and can be used freely. The simulation platform supports flexible specification of sensor suites, environmental conditions, full control of all static and dynamic actors, maps generation and much more. [Link to Carla](https://carla.org/)

## Scenario Selection

### Maps

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

### Traffic Scenarios

**Traffic and other entities** : The agent needs to navigate various road traffic rules and interact appropriately with other vehicles, pedestrians, cyclists, etc.

**Traffic intersections** : The agent needs to comprehensively interact with various information in the intersection road structure.

**Roundabout** : The agent needs to comprehensively interact with various information in the roundabout road structure.

**Lane Change** : The agent needs to perform lane-changing operations at the right time to reasonably navigate the prescribed route at key intersections.

![pic](https://github.com/xzh0312/DaViNCi/blob/master/imgs/TrafficScenarios.png?raw=true)






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
