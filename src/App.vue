<template>
  <div>
    <h2>Force-Direct Graph</h2>
    <svg width="960" height="600" class="container-border"></svg>
  </div>
</template>
<script>
import * as d3 from "d3";
export default {
  mounted() {
    let margin = { top: 60, bottom: 60, left: 60, right: 60 };
    let svg = d3.select("svg");
    let width = svg.attr("width");
    let height = svg.attr("height");
    let g = svg
      .append("g")
      .attr("transform", "translate(" + margin.top + "," + margin.left + ")");
    // prepare data
    // node set
    let nodes = [
      { name: "Hunan Shaoyang" },
      { name: "Shandong Taian" },
      { name: "Guangdong Yangjiang" },
      { name: "Shanxi Taiyuan" },
      { name: "bright" },
      { name: "Li hau tan" },
      { name: "Misbah" },
      { name: "Xiao Ming" },
      { name: "Group leader" },
    ];
    // edge set
    let edges = [
      { source: 0, target: 4, relation: "hometown", value: 1.3 },
      { source: 4, target: 5, relation: "Roommate", value: 1 },
      { source: 4, target: 6, relation: "Roommate", value: 1 },
      { source: 4, target: 7, relation: "Roommate", value: 1 },
      { source: 1, target: 6, relation: "hometown", value: 2 },
      { source: 2, target: 5, relation: "hometown", value: 0.9 },
      { source: 3, target: 7, relation: "hometown", value: 1 },
      { source: 5, target: 6, relation: "classmate", value: 1.6 },
      { source: 6, target: 7, relation: "friend", value: 0.7 },
      { source: 6, target: 8, relation: "responsibility", value: 2 },
    ];
    // Set a color scale
    let colorScale = d3
      .scaleOrdinal()
      .domain(d3.range(nodes.length))
      .range(d3.schemeCategory10);
    // Create a new force-directed graph
    let forceSimulation = d3
      .forceSimulation()
      .force("link", d3.forceLink())
      .force("charge", d3.forceManyBody())
      .force("center", d3.forceCenter());
    // Generate node data
    forceSimulation.nodes(nodes).on("tick", ticked);
    // Generate edge data
    forceSimulation
      .force("link")
      .links(edges)
      .distance(function (d) {
        // length of each side
        return d.value * 100;
      });
    // Set the center position of the graph
    forceSimulation
      .force("center")
      .x(width / 2)
      .y(height / 2);
    // // Vertex set, edge set
    // console.log(nodes)
    // console.log(edges)
    // draw edges
    let links = g
      .append("g")
      .selectAll("line")
      .data(edges)
      .enter()
      .append("line")
      .attr("stroke", function (d, i) {
        return colorScale(i);
      })
      .attr("stroke-width", 1);
    // text on the side
    let linksText = g
      .append("g")
      .selectAll("text")
      .data(edges)
      .enter()
      .append("text")
      .text(function (d) {
        return d.relation;
      });
    // Create group
    let gs = g
      .selectAll(".circleText")
      .data(nodes)
      .enter()
      .append("g")
      .attr("transform", function (d) {
        let cirX = d.x;
        let cirY = d.y;
        return "translate(" + cirX + "," + cirY + ")";
      })
      .call(d3.drag().on("start", started).on("drag", dragged).on("end", ended));
    // draw nodes
    gs.append("circle")
      .attr("r", 10)
      .attr("fill", function (d, i) {
        return colorScale(i);
      });
    // Word
    gs.append("text")
      .attr("x", -10)
      .attr("y", -20)
      .attr("dy", 10)
      .text(function (d) {
        return d.name;
      });
    // ticked
    function ticked() {
      links
        .attr("x1", function (d) {
          return d.source.x;
        })
        .attr("y1", function (d) {
          return d.source.y;
        })
        .attr("x2", function (d) {
          return d.target.x;
        })
        .attr("y2", function (d) {
          return d.target.y;
        });
      linksText
        .attr("x", function (d) {
          return (d.source.x + d.target.x) / 2;
        })
        .attr("y", function (d) {
          return (d.source.y + d.target.y) / 2;
        });
      gs.attr("transform", function (d) {
        return "translate(" + d.x + "," + d.y + ")";
      });
    }
    // drag
    function started(d) {
      if (!d3.event.active) {
        forceSimulation.alphaTarget(0.8).restart(); // Set the attenuation coefficient to simulate the movement process of the node position, the higher the value, the faster the movement, the value range [0, 1]
      }
      d.fx = d.x;
      d.fy = d.y;
    }
    function dragged(d) {
      d.fx = d3.event.x;
      d.fy = d3.event.y;
    }
    function ended(d) {
      if (!d3.event.active) {
        forceSimulation.alphaTarget(0);
      }
      d.fx = null;
      d.fy = null;
    }
  },
};
</script>
