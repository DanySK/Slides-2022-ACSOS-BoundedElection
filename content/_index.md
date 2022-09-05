 
+++

title = "Self-stabilising Priority-Based Multi-Leader Election and Network Partitioning"
description = "ACSOS 2022 presentation of Bounded Election"
outputs = ["Reveal"]

+++

{{% slide background-image="background.png" transition="slide" preload="true" %}}

<script>
  function hslToHex(h, s, l) {
    l /= 100;
    const a = s * Math.min(l, 1 - l) / 100;
    const f = n => {
      const k = (n + h / 30) % 12;
      const color = l - a * Math.max(Math.min(k - 3, 9 - k, 1), -1);
      return Math.round(255 * color).toString(16).padStart(2, '0');   // convert to Hex and prefix "0" if needed
    };
    return `#${f(0)}${f(8)}${f(4)}`;
  }
</script>

# Self-stabilising Priority-Based Multi-Leader Election and Network Partitioning

### [**Danilo Pianini**](mailto:danilo.pianini@unibo.it), [Roberto Casadei](mailto:roby.casadei@unibo.it), [Mirko Viroli](mailto:mirko.viroli@unibo.it)

{{% note %}}
Hello everyone

this work introduces a novel network partitioning and multi-leader election algorithm

I'd like to start by dissecting the title
{{% /note %}}

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

# <span style="color: #ff5d65ff">Self-stabilising</span> <span style="color: #8d60ffff">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff50ff">and Network Partitioning</span>

{{% note %}}
Let us start from the core, leader election
{{% /note %}}

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ff40">Priority-Based</span> <span style="color: #57ffe640">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

<script src="https://cdn.anychart.com/releases/8.8.0/js/anychart-core.min.js"></script>
<script src="https://cdn.anychart.com/releases/8.8.0/js/anychart-graph.min.js"></script>
<script src="https://cdn.anychart.com/releases/8.8.0/js/anychart-data-adapter.min.js"></script>
<div class="chart" id="container"></div>
<script>
anychart.data.loadJsonFile('network.json', function (data) {
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.hovered().stroke("5 #00ff00")
  edges.selected().stroke("5 #00ff00")
  edges.tooltip().enabled(false)
  var nodes = chart.nodes();
  nodes.height(60)
  nodes.fill("#1f2428")
  nodes.hovered().fill("#ffffff")
  nodes.hovered().stroke("5 #00ff00")
  nodes.selected().fill("#ffffff")
  nodes.selected().stroke("5 #00ff00")
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  var hoveredLabels = nodes.hovered().labels()
  var selectedLabels = nodes.selected().labels()
  labels.format("{%id}");
  labels.fontSize(20);
  labels.fontWeight(600);
  labels.anchor("center")
  labels.position("center")
  hoveredLabels.enabled(false);
  selectedLabels.enabled(false);
  // hoveredLabels.fontColor("#000000")
  labels.fontColor("black")
  chart.container("container").draw();
})
</script>

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ff40">Priority-Based</span> <span style="color: #57ffe640">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

<div class="chart" id="leader"></div>
<script>
anychart.data.loadJsonFile('network.json', function (data) {
  target = data.nodes.find((node) => node.id == "16")
  target.normal = { shape: "star5", height: "130", fill: "#fcd515", stroke: "5 #fc7e18" }
  target.selected = target.normal
  target.hovered = target.normal
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.hovered().stroke("5 #00ff00")
  edges.selected().stroke("5 #00ff00")
  edges.tooltip().enabled(false)
  var nodes = chart.nodes();
  nodes.height(60)
  nodes.fill("#1f2428")
  nodes.hovered().fill("#ffffff")
  nodes.hovered().stroke("5 #00ff00")
  nodes.selected().fill("#ffffff")
  nodes.selected().stroke("5 #00ff00")
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  var hoveredLabels = nodes.hovered().labels()
  var selectedLabels = nodes.selected().labels()
  labels.format("{%id}");
  labels.fontSize(20);
  labels.fontWeight(600);
  labels.anchor("center")
  labels.position("center")
  hoveredLabels.enabled(false);
  selectedLabels.enabled(false);
  // hoveredLabels.fontColor("#000000")
  labels.fontColor("black")
  chart.container("leader").draw();
})
</script>

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ff40">Priority-Based</span> <span style="color: #57ffe640">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

* {{% fragment %}} *Break symmetry* in peer networks {{% /fragment %}}
  * {{% fragment %}} Base block to create algorithms that require an "initial point" {{% /fragment %}}
* {{% fragment %}} Send (*summarize*) data to a sink {{% /fragment %}}
* {{% fragment %}} Find a *coordinator* for the whole network {{% /fragment %}}
* {{% fragment %}} Find a *single access point* for the whole network {{% /fragment %}}

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ff40">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

{{% fragment %}}

<div class="chart" id="multileader"></div>
<script>
anychart.data.loadJsonFile('network.json', function (data) {
  targets = data.nodes.filter((node) => ["18", "2", "27", "4"].includes(node.id) )
  targets.forEach((target, index) => {
    target.normal = {
      shape: "star10",
      height: "70",
      fill: hslToHex(index * 360 / targets.length, 100, 85),
      stroke: `5 ${hslToHex(index * 360 / targets.length, 100, 50)}`
    }
    target.selected = target.normal
    target.hovered = target.normal
  })
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.selected().stroke("5 #00ff00")
  edges.tooltip().enabled(false)
  var nodes = chart.nodes();
  nodes.height(60)
  nodes.fill("#1f2428")
  nodes.hovered().stroke("5 #00ff00")
  nodes.selected().fill("#ffffff")
  nodes.selected().stroke("5 #00ff00")
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  labels.format("{%id}");
  labels.anchor("center")
  labels.position("center")
  labels.fontSize(20);
  labels.fontWeight(600);
  chart.interactivity().enabled(false)
  chart.container("multileader").draw();
})
</script>

{{% /fragment %}}

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ff40">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

* {{% fragment %}} Base block to build *multi-scale* network overlays {{% /fragment %}}
* {{% fragment %}} Send (*summarize*) data to a *local* sink {{% /fragment %}}
  * {{% fragment %}} e.g., for later global summarization {{% /fragment %}}
* {{% fragment %}} Find a *coordinator* for the local portion of the network {{% /fragment %}}
* {{% fragment %}} Intercept phenomena whose *scale is neither device-local nor global*  {{% /fragment %}}
  * {{% fragment %}} e.g., non-spotty heavy rain may cause flooding {{% /fragment %}}

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ff40">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

<div class="chart" id="multileader2"></div>
<script>
anychart.data.loadJsonFile('network.json', function (data) {
  targets = data.nodes.filter((node) => ["18", "2", "27", "4"].includes(node.id) )
  targets.forEach((target, index) => {
    target.normal = {
      shape: "star10",
      height: "70",
      fill: hslToHex(index * 360 / targets.length, 100, 85),
      stroke: `5 ${hslToHex(index * 360 / targets.length, 100, 50)}`
    }
    target.selected = target.normal
    target.hovered = target.normal
  })
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.hovered().stroke("5 #00ff00")
  edges.selected().stroke("5 #00ff00")
  edges.tooltip().enabled(false)
  var nodes = chart.nodes();
  nodes.height(60)
  nodes.fill("#1f2428")
  nodes.hovered().fill("#ffffff")
  nodes.hovered().stroke("5 #00ff00")
  nodes.selected().fill("#ffffff")
  nodes.selected().stroke("5 #00ff00")
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  var hoveredLabels = nodes.hovered().labels()
  var selectedLabels = nodes.selected().labels()
  labels.format("{%id}");
  labels.fontSize(20);
  labels.fontWeight(600);
  labels.anchor("center")
  labels.position("center")
  hoveredLabels.enabled(true);
  selectedLabels.enabled(false);
  // hoveredLabels.fontColor("#000000")
  labels.fontColor("black")
  chart.interactivity().enabled(false)
  chart.container("multileader2").draw();
})
</script>

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ff40">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

<div class="chart" id="symmetric"></div>
<script>
anychart.data.loadJsonFile('network.json', function (data) {
  var smartphone = { src: "high-battery.png" }
  targets = data.nodes.filter((node) => ["18", "2", "27", "4"].includes(node.id) )
  targets.forEach((target, index) => {
    target.normal = {
      shape: "star10",
      height: "70",
      fill: smartphone,
      stroke: `5 ${hslToHex(index * 360 / targets.length, 100, 50)}`
    }
    target.selected = target.normal
    target.hovered = target.normal
  })
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.hovered().stroke("5 #00ff00")
  edges.selected().stroke("5 #00ff00")
  edges.tooltip().enabled(false)
  var nodes = chart.nodes();
  nodes.height(60)
  var fill = smartphone
  nodes.fill(fill)
  nodes.hovered().fill(fill)
  nodes.hovered().stroke("5 #00ff00")
  nodes.selected().fill(fill)
  nodes.selected().stroke("5 #00ff00")
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  var hoveredLabels = nodes.hovered().labels()
  var selectedLabels = nodes.selected().labels()
  labels.format("{%id}");
  labels.fontSize(20);
  labels.fontWeight(600);
  labels.anchor("center")
  labels.position("center")
  hoveredLabels.enabled(true);
  selectedLabels.enabled(false);
  // hoveredLabels.fontColor("#000000")
  labels.fontColor("black")
  chart.interactivity().enabled(false)
  chart.container("symmetric").draw();
})
</script>

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ff40">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

<div class="chart" id="asymmetric"></div>
<script>
anychart.data.loadJsonFile('network.json', function (data) {
  var lowBattery = { src: "low-battery.png" }
  targets = data.nodes.filter((node) => ["18", "2", "27", "4"].includes(node.id) )
  targets.forEach((target, index) => {
    target.normal = {
      shape: "star10",
      height: "70",
      fill: lowBattery,
      stroke: `5 ${hslToHex(index * 360 / targets.length, 100, 50)}`
    }
    target.selected = target.normal
    target.hovered = target.normal
  })
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edge = chart.group("edge")
  var edgeFill = { src: "high-battery.png" }
  edge.normal().fill(edgeFill)
  edge.hovered().fill(edgeFill)
  edge.selected().fill(edgeFill)
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.hovered().stroke("5 #00ff00")
  edges.selected().stroke("5 #00ff00")
  edges.tooltip().enabled(false)
  var nodes = chart.nodes();
  nodes.height(60)
  var fill = lowBattery
  nodes.fill(fill)
  nodes.hovered().fill(fill)
  nodes.hovered().stroke("5 #00ff00")
  nodes.selected().fill(fill)
  nodes.selected().stroke("5 #00ff00")
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  var hoveredLabels = nodes.hovered().labels()
  var selectedLabels = nodes.selected().labels()
  labels.format("{%id}");
  labels.fontSize(20);
  labels.fontWeight(600);
  labels.anchor("center")
  labels.position("center")
  hoveredLabels.enabled(true);
  selectedLabels.enabled(false);
  // hoveredLabels.fontColor("#000000")
  labels.fontColor("black")
  chart.interactivity().enabled(false)
  chart.container("asymmetric").draw();
})
</script>

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ffff">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

<div class="chart" id="priority"></div>
<script>
anychart.data.loadJsonFile('network.json', function (data) {
  var highBattery = { src: "high-battery.png" }
  var lowBattery = { src: "low-battery.png" }
  targets = data.nodes.filter((node) => node.group == "edge" )
  targets.forEach((target, index) => {
        target.normal = {
      shape: "star10",
      height: "70",
      fill: highBattery,
      stroke: `5 ${hslToHex(index * 360 / targets.length, 100, 50)}`
    }
    target.selected = target.normal
    target.hovered = target.normal
  })
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edge = chart.group("edge")
  var edgeFill = { src: "high-battery.png" }
  edge.normal().fill(edgeFill)
  edge.hovered().fill(edgeFill)
  edge.selected().fill(edgeFill)
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.hovered().stroke("5 #00ff00")
  edges.selected().stroke("5 #00ff00")
  edges.tooltip().enabled(false)
  var nodes = chart.nodes();
  nodes.height(60)
  var fill = lowBattery
  nodes.fill(fill)
  nodes.hovered().fill(fill)
  nodes.hovered().stroke("5 #00ff00")
  nodes.selected().fill(fill)
  nodes.selected().stroke("5 #00ff00")
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  var hoveredLabels = nodes.hovered().labels()
  var selectedLabels = nodes.selected().labels()
  labels.format("{%id}");
  labels.fontSize(20);
  labels.fontWeight(600);
  labels.anchor("center")
  labels.position("center")
  hoveredLabels.enabled(true);
  selectedLabels.enabled(false);
  // hoveredLabels.fontColor("#000000")
  labels.fontColor("black")
  chart.interactivity().enabled(false)
  chart.container("priority").draw();
})
</script>

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

# <span style="color: #ff5d6540">Self-stabilising</span> <span style="color: #8d60ffff">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

* Select leaders with a specified *criterion*
  * Assign demanding task to more *performant* nodes
  * Prefer static nodes over mobile ones for network *stability*
  * Coordinate activities in nodes with larger *data-rate*
  * *Balance energy consumption* by moving activities to high-battery nodes
* The leader set can change *dynamically*
  * Enabling *group tracking* of underlying spatio-temporal phenomena

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

## Self stabilisation

Ability to **converge**:
* {{% fragment %}} **to** *correct states* {{% /fragment %}}
* {{% fragment %}} **in** *finite time* {{% /fragment %}}
* {{% fragment %}} **from** *any arbitrary initial state* {{% /fragment %}}

{{% fragment %}} 
hence, in spite of transitory changes
(*resiliently*)
{{% /fragment %}}

<br>

{{% fragment %}}
> We call the system "self-stabilizing" if and only if,
regardless of the initial state and regardless of the
privilege selected each time for the next move, at least
one privilege will always be present and the system is
guaranteed to find itself in a legitimate state after a
finite number of moves
<cite>E. W. Dijkstra, "Self-stabilizing systems in spite of distributed control" **1974**</cite>

{{% /fragment %}}

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

#### Example of **not**-self-stabilising leader election

Despite *no changes* to the algorithm input,
the output *oscillates*

![](https://github.com/DanySK/experiment-2022-self-stab-leader-election/raw/master/s-unstable.gif)

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

# <span style="color: #ff5d65ff">Self-stabilising</span> <span style="color: #8d60ffff">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

* Inputs can change, the leader set *adapts* in finite time
  * As far as the inputs are self-stabilising expressions
* Same inputs $\Rightarrow$ Same output: *reproducible results*
* Convergence to a *stable state* guaranteed in *finite time*
* No formal guarantees on the *transient*

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d65ff">Self-stabilising</span> <span style="color: #8d60ffff">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff5040">and Network Partitioning</span>

<div class="chart" id="selfstab"></div>
<script>
anychart.data.loadJsonFile('network.json', function (data) {
  targets = data.nodes.filter((node) => node.group == "edge" )
  targets.forEach((target, index) => {
    target.normal = {
      shape: "star10",
      height: "70",
      fill: hslToHex(index * 360 / targets.length, 100, 25),
      stroke: `5 ${hslToHex(index * 360 / targets.length, 100, 50)}`
    }
    target.selected = target.normal
    target.hovered = target.normal
  })
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edge = chart.group("edge")
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.tooltip().enabled(false)
  var nodes = chart.nodes();
  nodes.height(60)
  var fill = "black"
  nodes.fill(fill)
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  var hoveredLabels = nodes.hovered().labels()
  var selectedLabels = nodes.selected().labels()
  labels.format("{%id}");
  labels.fontSize(20);
  labels.fontWeight(600);
  labels.anchor("center")
  labels.position("center")
  hoveredLabels.enabled(true);
  selectedLabels.enabled(false);
  // hoveredLabels.fontColor("#000000")
  labels.fontColor("black")
  chart.interactivity().enabled(false)
  chart.container("selfstab").draw();
})
</script>

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# <span style="color: #ff5d65ff">Self-stabilising</span> <span style="color: #8d60ffff">Priority-Based</span> <span style="color: #57ffe6ff">Multi-</span>Leader Election <span style="color: #cbff50ff">and Network Partitioning</span>

<div class="chart" id="partition"></div>
<script>
anychart.data.loadJsonFile('network.json', function (data) {
  // Find "thick" devices
  var edgeNodes = data.nodes.filter((node) => node.group == "edge" )
  // Based on their count, compute the leader color
  edgeNodes.forEach((target, index) => {
    var strokeColor = hslToHex(index * 360 / edgeNodes.length, 100, 50)
    target.normal = {
      shape: "star10",
      height: "70",
      fill: hslToHex(index * 360 / edgeNodes.length, 100, 25),
      stroke: `5 ${strokeColor}`
    }
    target.selected = target.normal
    target.hovered = target.normal
    // this additional field holds the actual partition color for later
    target.colorGroup = strokeColor
  })
  // Distance between nodes.
  // Customized to double the distance of non-neighbors
  function distance(node1, node2) {
    return Math.hypot(node2.y - node1.y, node2.x - node1.x) *
      (data.edges.some(edge =>
          edge.from === node1.id && edge.to === node2.id
          || edge.from === node2.id && edge.to === node1.id
        ) ? 1 : 2
      )
  }
  // Now, color very node
  data.nodes.forEach(node => {
    // Find the closest leader
    var nearest = edgeNodes.reduce((best, current) =>
      distance(node, current) < distance(node, best) ? current : best
    )
    node.normal = node.normal == null ? {} : node.normal
    node.normal.fill = nearest.normal.fill
    node.normal.stroke = nearest.normal.stroke
    // Save the actual partition color
    node.colorGroup = nearest.colorGroup
    node.selected = node.normal
    node.hovered = node.normal
  })
  // Super-cool gradient on edges!
  data.edges.forEach(edge => {
    // Find the nodes this edge connects 
    var from = data.nodes.find(node => node.id === edge.from)
    var to = data.nodes.find(node => node.id === edge.to)
    // Compute the angle between them
    var angle = -Math.atan2(to.y - from.y, to.x - from.x) * 180 / Math.PI
    edge.normal = {
      // See: https://api.anychart.com/v8/anychart.graphics.vector.LinearGradientStroke
      stroke: {
        keys: [
          { color: from.colorGroup, offset: .4 },
          { color: to.colorGroup, offset: .6 }
        ],
        angle: angle,
        thickness: 5
      }
    }
  })
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edge = chart.group("edge")
  var nodes = chart.nodes();
  nodes.height(60)
  var fill = "black"
  nodes.fill(fill)
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  var hoveredLabels = nodes.hovered().labels()
  var selectedLabels = nodes.selected().labels()
  labels.format("{%id}");
  labels.fontSize(20);
  labels.fontWeight(600);
  labels.anchor("center")
  labels.position("center")
  labels.enabled(false)
  hoveredLabels.enabled(false);
  selectedLabels.enabled(false);
  // hoveredLabels.fontColor("#000000")
  labels.fontColor("black")
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.hovered().stroke("5 #00ff00")
  edges.selected().stroke("5 #00ff00")
  edges.tooltip().enabled(false)
  chart.interactivity().enabled(false)
  chart.container("partition").draw();
})
</script>

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

# What is outside there

{{% fragment %}}
* In J. Beal and M. Viroli, **Building blocks for aggregate programming of
self-organising applications**, *FoCAS 2014* 
  * "Sparse spatial choice" or just *S*, designed for *multi-leader election*
  * Based *expanding areas of influence* and *competition at the border*
  * Likely *self-stabilising*, but *never proven*
{{% /fragment %}}

{{% fragment %}}
* In Y. Mo, J. Beal, and S. Dasgupta, **An aggregate computing approach
to self-stabilizing leader election**, *eCAS 2018*
  * Focused on single leader election
  * Relies on *network diameter estimation* feedback loops
  * *Self-stabilising* (combines self-stabilising blocks in a self-stabilising manner)
{{% /fragment %}}

{{% fragment %}}
* In Y. Mo, G. Audrito, S. Dasgupta, and J. Beal, **A resilient
leader election algorithm using aggregate computing blocks**, *to appear*
  * *Extension* of the former
  * Tunable for *multi-leader* election, but with a *partition size proportional to the network diameter*
  * Still relies on *diameter estimation*, *collection*, and *propagation*
{{% /fragment %}} 

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Goals

* {{% fragment %}} *Multi-leader* election {{% /fragment %}}
* {{% fragment %}} Explicit *priority control* {{% /fragment %}}
* {{% fragment %}} Explicit *partition size* {{% /fragment %}}
* {{% fragment %}} *Adaptable distance metric* {{% /fragment %}}
  * {{% fragment %}} Must work in any metric space, regardless if it maps a phyisical space or not {{% /fragment %}}
* {{% fragment %}} Possibly, *no feedback loops* {{% /fragment %}}
  * {{% fragment %}} Diffusing information is more robust than accumulating it {{% /fragment %}}
  * {{% fragment %}} Accumulation + diffusion weighs on performance and robustness {{% /fragment %}}

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea

1. {{% fragment %}} Make *yourself* a *candidate* leader {{% /fragment %}}
1. {{% fragment %}} Select the *best leader* including information from *neighbors* (if any) {{% /fragment %}}
2. {{% fragment %}} *Gossip* the information on the best leader to all neighbors {{% /fragment %}}
3. {{% fragment %}} If the best leader is too far away, *re-run* the algorithm ignoring nodes managed by the best leader{{% /fragment %}}
  * {{% fragment %}} Basically, *remove the first partition from the domain* {{% /fragment %}}

---

<script>
function chartWithEnvironment(environmentFunction, container) {
  anychart.data.loadJsonFile('network.json', function (data) {
  // Find "thick" devices
  data.nodes.sort((a, b) => Number(a.id) > Number(b.id) ? -1 : 1)
  var environment = environmentFunction(data)
  var leaderIds = new Set(environment.values())
  // Based on their count, compute the leader color
  var leaderIndex = 0
  var leaders = []
  data.nodes.forEach(target => {
    if (leaderIds.has(target.id)) {
      // Perceived leader
      var strokeColor = hslToHex(leaderIndex * 360 / leaderIds.size, 100, 50)
      target.colorGroup = strokeColor
      if (environment.get(target.id) === target.id) {
        // True leader
        target.normal = {
          shape: "star10",
          height: "70",
          fill: hslToHex(leaderIndex * 360 / leaderIds.size, 100, 25),
          stroke: `5 ${strokeColor}`
        }
        target.selected = target.normal
        target.hovered = target.normal
      }
      leaderIndex++
      leaders.push(target)
    }
  })
  // Now, color very node
  var noInfo = { id: -1, x: 1e9, y: 1e9, normal: { fill: '#1f2428', stroke: '5 white' }, colorGroup: 'white' }
  data.nodes.reverse().forEach(node => {
    // Find the closest leader
    var leaderId = environment.get(node.id)
    var leadersLeader = typeof leaderId === 'undefined' ? undefined : environment.get(leaderId)
    var consistent = leadersLeader !== 'undefined' && leaderId === leadersLeader
    var leaderNode = leaders.find(it => it.id === leaderId) || noInfo
    node.normal = node.normal || {}
    node.colorGroup = leaderNode.colorGroup
    if (consistent) {
      node.normal.fill = leaderNode.normal.fill
      node.normal.stroke = leaderNode.normal.stroke
    } else {
      node.normal.fill = 'gray'
      node.normal.stroke = `5 ${leaderNode.colorGroup}`
    }
    node.selected = node.normal
    node.hovered = node.normal
  })
  // Super-cool gradient on edges!
  data.edges.forEach(edge => {
    // Find the nodes this edge connects 
    var from = data.nodes.find(node => node.id === edge.from)
    var to = data.nodes.find(node => node.id === edge.to)
    // Compute the angle between them
    var angle = -Math.atan2(to.y - from.y, to.x - from.x) * 180 / Math.PI
    edge.normal = {
      // See: https://api.anychart.com/v8/anychart.graphics.vector.LinearGradientStroke
      stroke: {
        keys: [
          { color: from.colorGroup, offset: .4 },
          { color: to.colorGroup, offset: .6 }
        ],
        angle: angle,
        thickness: 5
      }
    }
  })
  var chart = anychart.graph(data);
  chart.layout().type("fixed");
  chart.background().fill("#fff0f030");
  var edge = chart.group("edge")
  var nodes = chart.nodes();
  nodes.height(60)
  var fill = "black"
  nodes.fill(fill)
  nodes.hovered().fill(fill)
  nodes.hovered().stroke("5 #00ff00")
  nodes.selected().fill(fill)
  nodes.selected().stroke("5 #00ff00")
  nodes.stroke("4 #ffffff")
  nodes.tooltip().enabled(false)
  var labels = nodes.labels()
  var hoveredLabels = nodes.hovered().labels()
  var selectedLabels = nodes.selected().labels()
  labels.enabled(true)
  labels.format("{%id}");
  labels.fontColor('white')
  labels.fontFamily('Inconsolata')
  labels.fontSize(35);
  labels.fontWeight(600);
  labels.position("center")
  labels.anchor("center")
  labels.offsetY(-1000)
  hoveredLabels.enabled(true);
  selectedLabels.enabled(false);
  // hoveredLabels.fontColor("#000000")
  var edges = chart.edges()
  edges.stroke("4 #ffffff")
  edges.hovered().stroke("4 white")
  // edges.selected().stroke("5 #00ff00")
  edges.tooltip().enabled(false)
  chart.interactivity().enabled(false)
  chart.container(container).draw();
})
}
</script>

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, I

<div class="chart" id="trivial0"></div>
<script>
  chartWithEnvironment(data => new Map(), "trivial0")
</script>

The network begins the computation

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, II

<div class="chart" id="trivial1"></div>
<script>
  chartWithEnvironment(
    data => new Map(data.nodes.map(node => [node.id, node.id])),
    "trivial1"
  )
</script>

Every device knows only itself, it assumes leadership and tells neighbors

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, III

<div class="chart" id="trivial2"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "30"],
      ["2", "26"],
      ["3", "26"],
      ["4", "25"],
      ["5", "20"],
      ["6", "28"],
      ["7", "22"],
      ["8", "18"],
      ["9", "31"],
      ["10", "15"],
      ["11", "30"],
      ["12", "18"],
      ["13", "28"],
      ["14", "25"],
      ["15", "17"],
      ["16", "28"],
      ["17", "27"],
      ["18", "20"],
      ["19", "22"],
      ["20", "31"],
      ["21", "29"],
      ["22", "25"],
      ["23", "27"],
      ["24", "29"],
      ["25", "25"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial2"
  )
</script>

Gossiping of the best leader information induces the creation of partitions

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, IV

<div class="chart" id="trivial3"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "27"],
      ["5", "31"],
      ["6", "28"],
      ["7", "25"],
      ["8", "30"],
      ["9", "31"],
      ["10", "25"],
      ["11", "31"],
      ["12", "20"],
      ["13", "28"],
      ["14", "25"],
      ["15", "25"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial3"
  )
</script>

Regions form, but the spreading now reaches the maximum allowed dimension (2 hops)

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, V

<div class="chart" id="trivial4"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["-2", "28"],
      ["3", "31"],
      ["-4", "27"],
      ["5", "31"],
      ["-6", "28"],
      ["7", "25"],
      ["-8", "8"],
      ["9", "31"],
      ["10", "25"],
      ["11", "31"],
      ["-12", "20"],
      ["13", "28"],
      ["14", "25"],
      ["15", "25"],
      ["-16", "28"],
      ["-17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["-26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial4"
  )
</script>

Nodes that know they can not join the best leader they know restart the algorithm

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, VI

<div class="chart" id="trivial5"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["-0", "28"],
      ["1", "31"],
      ["2", "2"],
      ["3", "31"],
      ["4", "4"],
      ["5", "31"],
      ["6", "6"],
      ["7", "25"],
      ["8", "8"],
      ["9", "31"],
      ["-10", "25"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["-14", "25"],
      ["-15", "25"],
      ["16", "16"],
      ["17", "17"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["-23", "28"],
      ["24", "31"],
      ["-25", "28"],
      ["26", "26"],
      ["-27", "28"],
      ["-28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial5"
  )
</script>

The "second best" leader search wave moves across the network

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, VII

<div class="chart" id="trivial6"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "0"],
      ["1", "31"],
      ["2", "26"],
      ["3", "31"],
      ["4", "17"],
      ["5", "31"],
      ["6", "26"],
      ["-7", "25"],
      ["8", "12"],
      ["9", "31"],
      ["10", "10"],
      ["11", "31"],
      ["12", "12"],
      ["-13", "28"],
      ["14", "14"],
      ["15", "15"],
      ["16", "26"],
      ["17", "17"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["-22", "28"],
      ["23", "23"],
      ["24", "31"],
      ["25", "25"],
      ["26", "26"],
      ["27", "27"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial6"
  )
</script>

Previous areas get disrupted!

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, VIII

<div class="chart" id="trivial7"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "26"],
      ["3", "31"],
      ["4", "25"],
      ["5", "31"],
      ["6", "28"],
      ["7", "7"],
      ["8", "12"],
      ["9", "31"],
      ["10", "17"],
      ["11", "31"],
      ["12", "12"],
      ["13", "13"],
      ["14", "25"],
      ["15", "17"],
      ["16", "28"],
      ["17", "27"],
      ["18", "31"],
      ["-19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "22"],
      ["23", "27"],
      ["24", "31"],
      ["25", "25"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial7"
  )
</script>

New regions begin to form again in parts of the network that can't be controlled by the best node

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, IX

<div class="chart" id="trivial8"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "25"],
      ["5", "31"],
      ["6", "28"],
      ["7", "25"],
      ["8", "12"],
      ["9", "31"],
      ["10", "25"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["14", "25"],
      ["15", "25"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "19"],
      ["20", "31"],
      ["21", "31"],
      ["22", "25"],
      ["23", "28"],
      ["24", "31"],
      ["25", "25"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial8"
  )
</script>

All nodes joined the "second wave" of gossiping...

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, X

<div class="chart" id="trivial9"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["-4", "25"],
      ["5", "31"],
      ["6", "28"],
      ["7", "25"],
      ["8", "12"],
      ["9", "31"],
      ["10", "25"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["14", "25"],
      ["-15", "25"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial9"
  )
</script>

...which does not cover the whole network, so a third restart happens

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, XI

<div class="chart" id="trivial10"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "4"],
      ["5", "31"],
      ["6", "28"],
      ["-7", "25"],
      ["8", "12"],
      ["9", "31"],
      ["-10", "25"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["-14", "25"],
      ["15", "15"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial10"
  )
</script>

All nodes are now either stable or in their last recursion of the algorithm

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, XII

<div class="chart" id="trivial11"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "4"],
      ["5", "31"],
      ["6", "28"],
      ["7", "7"],
      ["8", "12"],
      ["9", "31"],
      ["10", "10"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["14", "14"],
      ["15", "15"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial11"
  )
</script>

Stable nodes do not propagate information on unstable partitions, slowing down convergence

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, XIII

<div class="chart" id="trivial12"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "14"],
      ["5", "31"],
      ["6", "28"],
      ["7", "14"],
      ["8", "12"],
      ["9", "31"],
      ["10", "15"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["14", "15"],
      ["15", "15"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial12"
  )
</script>

In one step, stability will be reached

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea running example, XIV

<div class="chart" id="trivial13"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "15"],
      ["5", "31"],
      ["6", "28"],
      ["7", "15"],
      ["8", "12"],
      ["9", "31"],
      ["10", "15"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["14", "15"],
      ["15", "15"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "trivial13"
  )
</script>

Done

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Trivial idea

{{% fragment %}}
**Pros**
* Works
* Uses solely diffusion, with no accumulation
* Can be implemented very succintly using frameworks such as aggregate computing
{{% /fragment %}}

{{% fragment %}}
```haskell
def leaderElection(id, priority, radius, metric) {
  let best = gossip([priority, id], min).get(1) -- best candidacy in the neighborhood
  if (distanceToWithMetric(best == id, metric) <= radius) {
    best -- We can be managed by the best known leader
  } else { leaderElection(id, priority, radius, metric) }
}
```
{{% /fragment %}}

{{% fragment %}}
**Cons**
* It is *not self-stabilising* for many implementations (including the one above)
  * *Gossip* is not self-stabilising
  * It can be made self-stabilise by running overlapping replicates
* *Discards useful information* when re-stabilising!
  * We had information on the second-best leader that we re-computed...
{{% /fragment %}}

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Bounded Election

Instead recursively gossiping, *parallelise* the computation and *compete at the border*

{{% fragment %}}
1. Every device produces its *opinion on the best leader*, which includes:
    1. the leader’s priority;
    2. the distance to the leader;
    3. the leader's id
1. At the beginning, *every device candidates itself*
2. Every device $d$ observes the *best candidates in its neighbourhood*, discarding those that promote $d$ itself and those whose proposed leader is too far away.
3. The *new best leader* is the best between itself and the best (if any) of those acquired from the neighbourhood.
4. The best leader can be *communicated to the neighbours*.
{{% /fragment %}}

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Bounded Election running, I

<div class="chart" id="boundedElection0"></div>
<script>
  chartWithEnvironment(data => new Map(), "boundedElection0")
</script>

The network begins the computation

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Bounded Election running, II

<div class="chart" id="boundedElection1"></div>
<script>
  chartWithEnvironment(
    data => new Map(data.nodes.map(node => [node.id, node.id])),
    "boundedElection1"
  )
</script>

Every device knows only itself, it assumes leadership and tells neighbors

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Bounded Election running, III

<div class="chart" id="boundedElection2"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "30"],
      ["2", "26"],
      ["3", "26"],
      ["4", "25"],
      ["5", "20"],
      ["6", "28"],
      ["7", "22"],
      ["8", "18"],
      ["9", "31"],
      ["10", "15"],
      ["11", "30"],
      ["12", "18"],
      ["13", "28"],
      ["14", "25"],
      ["15", "17"],
      ["16", "28"],
      ["17", "27"],
      ["18", "20"],
      ["19", "22"],
      ["20", "31"],
      ["21", "29"],
      ["22", "25"],
      ["23", "27"],
      ["24", "29"],
      ["25", "25"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "boundedElection2"
  )
</script>

Partitions begin to form, several nodes remain in inconsistent state (gray-filled)

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Bounded Election running, IV

<div class="chart" id="boundedElection3"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "25"],
      ["5", "31"],
      ["6", "28"],
      ["7", "25"],
      ["8", "30"],
      ["9", "31"],
      ["10", "25"],
      ["11", "31"],
      ["12", "20"],
      ["13", "28"],
      ["14", "25"],
      ["15", "27"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "boundedElection3"
  )
</script>

The first two partitions are formed! 25/32 nodes reached stability in two rounds

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Bounded Election running, V

<div class="chart" id="boundedElection4"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "25"],
      ["5", "31"],
      ["6", "28"],
      ["7", "25"],
      ["8", "8"],
      ["9", "31"],
      ["10", "25"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["14", "25"],
      ["15", "25"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "boundedElection4"
  )
</script>

Stabilizing the outskirts of the network takes longer, especially if previous information is useless

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Bounded Election running, VI

<div class="chart" id="boundedElection5"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "4"],
      ["5", "31"],
      ["6", "28"],
      ["7", "7"],
      ["8", "12"],
      ["9", "31"],
      ["10", "10"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["14", "14"],
      ["15", "15"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "boundedElection5"
  )
</script>

In unlucky cases, several nodes may reset when their former leader is embodied in another partition

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Bounded Election running, VII

<div class="chart" id="boundedElection6"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "15"],
      ["5", "31"],
      ["6", "28"],
      ["7", "14"],
      ["8", "12"],
      ["9", "31"],
      ["10", "15"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["14", "15"],
      ["15", "15"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "boundedElection6"
  )
</script>

In the worst case, a partition restabilises in the time required for the new leader to reach the farthest node

---

{{% slide background-image="background.png" transition="none" preload="true"  %}}

# Bounded Election running, VIII

<div class="chart" id="boundedElection7"></div>
<script>
  chartWithEnvironment(
    data => new Map([
      ["0", "28"],
      ["1", "31"],
      ["2", "28"],
      ["3", "31"],
      ["4", "15"],
      ["5", "31"],
      ["6", "28"],
      ["7", "15"],
      ["8", "12"],
      ["9", "31"],
      ["10", "15"],
      ["11", "31"],
      ["12", "12"],
      ["13", "28"],
      ["14", "15"],
      ["15", "15"],
      ["16", "28"],
      ["17", "28"],
      ["18", "31"],
      ["19", "28"],
      ["20", "31"],
      ["21", "31"],
      ["22", "28"],
      ["23", "28"],
      ["24", "31"],
      ["25", "28"],
      ["26", "28"],
      ["27", "28"],
      ["28", "28"],
      ["29", "31"],
      ["30", "31"],
      ["31", "31"],
    ]),
    "boundedElection7"
  )
</script>

The network is thus stable in eight rounds (compared to 14 of the recursive version)

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

# Bounded Election

**Pros**
* {{% fragment %}} *Works* {{% /fragment %}}
* {{% fragment %}}Uses solely diffusion, with *no accumulation* {{% /fragment %}}
* {{% fragment %}} Can be *implemented succintly* using frameworks such as aggregate computing {{% /fragment %}}
* {{% fragment %}}Stabilises multiple partitions in *parallel* {{% /fragment %}}
* {{% fragment %}}Supports *prioritisation* and control of the *distance metric* {{% /fragment %}}
* {{% fragment %}}It **proven to be _self-stabilising_** (more in a moment) {{% /fragment %}}

**Cons**

{{% fragment %}}
* As per the recursive version, there are *pathological cases*
  * Can be concocted in simulation
  * The base idea is to place high priority nodes inconveniently, and let the second-highest priority node move continuously across the border of the highest-priority partition, triggering continuous re-adaptation in the whole network
{{% /fragment %}}

---

{{% section %}}

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

## Self-stabilisation of Bounded Election

Proof structure:

1. Pick a demonstrably *self-stabilising fragment*
  <br> $\Rightarrow$ (the *minimising `share`*)
2. *Re-write* parts of it in a way that *preserves self-stabilisation*
3. Until you get to an implementation of *Bounded Election*

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

## Proof

#### The minimising `share` pattern

We start from a [proven self-stabilising fragment](https://lmcs.episciences.org/6816):

```
share(x <- e) { fR(minHoodLoc(fMP(x, s̄), e), x)}
```

Where `x` is a neighbour field, namely a mapping from each device in the neighbourhood of the executing device with some value.

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

### Rewrite: identity for `fR` 

* `fR(x, p)` is a raising function w.r.t. partial orders of `x` and `p`, with `p` previous value of `x`
  * Identity over `x` is a [trivially valid replacement](https://doi.org/10.1145/3177774)

```
share(x <- e) { fR(minHoodLoc(fMP(x, s̄), e), x)}
```
$\big\Downarrow$
```
share(x <- e) { minHoodLoc(fMP(x, s̄), e) }
```

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

### Rewrite: local candidacy for `e` 

* `e` is any self-stabilising expression
* We rewrite it with a triple representing the local candidacy, where:
  * `value` is the local priority
  * `distance` is the distance from the leader
  * `lead` is the unique identifier of the local node
* Moreover, we assume in our scope:
  * a `priority` value representing the leader strength
  * an `id` value representing a unique identifier for the local node

```
share(x <- e) { minHoodLoc(fMP(x, s̄), e) }
```
$\big\Downarrow$
```
local <- (value: -priority, distance: 0, lead: id)
share(x <- local) { minHoodLoc(fMP(x, s̄), local) }
```

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

### Rewrite: return the `id`

* We want to return only the leader `id`
* It does not alter the `share` pattern

```
local <- (value: -priority, distance: 0, lead: id)
share(x <- local) { minHoodLoc(fMP(x, s̄), local) }
```
$\big\Downarrow$
```
local <- (value: -priority, distance: 0, lead: id)
share(x <- local) { minHoodLoc(fMP(x, s̄), local) }.lead
```

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

### Rewrite: `fMP`

* `fMp(x, s̄)` is a monotonic progressive function of `x`
* `s̄` are additional arguments that can be passed to the function as far as they are self-stabilising expressions
* We assume a `metric()` function returning a field of distances from each neighbour
* We assume a maximum partition `radius`
* We replace it with a `map` and `filter` functions that:
  * add the distance to valid neighbor candidacies
  * remove invalid candidacies
* It cannot return a field with a value lesser than the lesser value of `x`, and it is thus a valid replacement

```
local <- (value: -priority, distance: 0, lead: id)
share(x <- local) { minHoodLoc(fMP(x, s̄), local) }.lead
```
$\big\Downarrow$
```
local <- (value: -priority, distance: 0, lead: id)
share(x <- local) {
  minHoodLoc(
    x.map { (it.value, it.distance + metric(), id }
      .filter{ it.distance <= radius && it.id != local.id },
    local
  )
}.lead
```

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

### Minimisation

* `minHoodLoc` is a function selecting the minimum among the field values and the provided local value
* We assume triples to be ordered based on their components

No rewriting needed, and since:

```
local <- (value: -priority, distance: 0, lead: id)
share(x <- local) {
  minHoodLoc(
    x.map { (it.value, it.distance + metric(), id }
      .filter{ it.distance <= radius && it.id != local.id },
    local
  )
}.lead
```

is a valid implementation of Bounded Election,

**Bounded Election is self stabilising**
{{% align-right %}} □ {{% /align-right %}} 

{{% /section %}}

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

# Evaluation

| Scale free | Edge | Random |
| --- | --- | --- |
| ![](complex-deg.png) | ![](edge.png) | ![](random.png) |
| Devices switch priority at runtime | Some devices are static | All devices move randomly
| Tests adaptation transitories  | Tests prioritization effectiveness | Searches for pathological cases

---

{{% section %}}

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

## Metric

Measuring *adaptation* is not so easy
* We concocted a metric of *instability*
* Informally:
  * how many times every node changed its leader over how many times it could have done so, in the last 10 seconds
  * **the lesser, the better**

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

### Formally

* let $I$ be the set of all possible device UIDs;
* let $l^d_t \in I$ be the leader selected in device $d$ at round $t$;
* let $T$ be the current round;
* for each device $d$, we consider the set $L^d_{TR} = \{ l^d_{T}, l^d_{T-1}, \dots, l^d_{T-R} \}$ comprising the leaders perceived in a mobile window spanning the last $R+1$ rounds;
* we then consider the $R$ couples of subsequent leaders
$S^d = \{ (l^d_i,\ l^d_j)\ |\ \forall j = i + 1;\ l^d_i, l^d_j \in R \}$;
* we define a function over 2-ples $c: I^2 \to \{0, 1\}$
$$
c((k_1, k_2))=
\begin{cases}
1,\ k_1 = k_2\\
0,\ \text{otherwise}\\
\end{cases}
$$
that outputs $1$ iff the elements of the tuple are equal;
* we then count how many times in the last $R + 1$ rounds the leader has not changed,
normalised on the considered number of rounds:
$C^d = \frac{\sum_{x \in S^d} c(x)}{|S^d|}$,
we consider this a measure of *local instability*;
* finally, we obtain our global metric of *global instability*
$Y = \frac{\sum_{d \in I} C^d}{|I|}$
by normalising the sum of the local contributions.

{{% /section %}}

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

## Compared algorithms

* *Bounded election* (`proposed`)
* Baseline: *recursive election* (`recursive`)
  * To measure if and how better parallelisation is
* Baseline: *sparse-choice* (`classic`)
  * To compare with the state of the art, as it is the closest and the only one using solely propagation (hence, likely the fastest)

{{% fragment %}}

## Details

* Means of 200 repetitions
* 100% reproducible, https://github.com/DanySK/experiment-2022-self-stab-leader-election
* Implemented in [Protelis](https://protelis.github.io/) and [Scafi](https://scafi.github.io/)
* Simulated in [Alchemist](https://alchemistsimulator.github.io/)

{{% /fragment %}}

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

## Scale-free network

**Expectation**: Sparse-choice and Bounded Election perform similarly, the recursive version is worse

{{% fragment %}}

![](barabasi.svg)

* **Bounded Election** has consistently the *fastest* convergence
* **All** algorithms *self-stabilised*
* The **recursive** version is very *sensible* to *changes in the leader priority* (using central leaders makes the algorithm outperform the classic one), Bounded election and Sparse-choice are not

{{% /fragment %}}

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

**Expectation**: Bounded Election performs best, the recursive version should outperform Sparse-choice or be close (prioritization of static nodes should compensate for occasional large disruptions)

## Asymmetric nodes

{{% fragment %}}

![](edge.svg)

* The algorithms behave as forecasted
* **Bounded Election** performs best, as it can select the stable leaders via *prioritization* and is faster than the recursive to *recover from disruptions*.
* **Sparse-choice**'s average performance is similar to the **recursive** one, but *more predictable*

{{% /fragment %}}

---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

**Expectation**: Nodes moving randomly favor **Sparse-choice**,
that should perform best as it minimises changes within partitions by competing only on the border.

## Randomly moving nodes

{{% fragment %}}

![](random.svg)

* The behaviour is *surprising*: **Bounded Election** *performs best* again
* **Sparse-choice** inner-partition stability seems to generate instability globally
  * Our current hypotesis is that, although the partition core is more stable in the competing solutions, most nodes are in the outskirts and do not enjoy it
  * Probably, with so much randomness, occasional network-wide disruptions are less impacting than continuous border adjustments

{{% /fragment %}}


---

{{% slide background-image="background.png" transition="slide" preload="true"  %}}

## Conclusion

**Bounded Election** is a *proven self-stabilising* algorithm
for *multi-leader election*
that induces *network partitioning* and
suppors explicit *prioritisation*
and *arbitrary metrics*.

* It *performs better* than other multi-leader election algorithms in most scenarios
* *Corner cases* where the behaviour is bad can get built
  * but it looks pretty *unlikely* that they occur

{{% fragment %}}

### What lies ahead

* More testing is needed to assess how frequently the configuration can induce unwanted behaviour
* Tackle cases where the behaviour is not ideal
* Towards a new family of self-stabilising leader election algorithms?

{{% /fragment %}}
