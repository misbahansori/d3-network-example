<template>
  <div class="flex items-center justify-center w-full min-h-screen">
    <svg class="w-full min-h-screen bg-gray-200" width="1000" height="600"></svg>
  </div>
</template>
<script>
import * as d3 from "d3";
export default {
  mounted() {
    const svg = d3.select("svg");
    const width = svg.attr("width");
    const height = svg.attr("height");

    const margin = { top: 60, bottom: 60, left: 60, right: 60 };

    const g = svg
      .append("g")
      .attr("transform", "translate(" + margin.top + "," + margin.left + ")");

    const nodes = [
      { id: 1, name: "Price Goyette", avatar: "1.jpeg" },
      { id: 2, name: "Giovanna Sawayn", avatar: "2.jpeg" },
      { id: 3, name: "Nils Considine", avatar: "3.jpeg" },
      { id: 4, name: "Henderson Rosenbaum", avatar: "4.jpeg" },
      { id: 5, name: "Myron Bergnaum", avatar: "5.jpeg" },
      { id: 6, name: "Crystel Gorczany", avatar: "6.jpeg" },
      { id: 7, name: "Ulises Morissette", avatar: "7.jpeg" },
      { id: 8, name: "Dahlia Wilkinson", avatar: "8.jpeg" },
      { id: 9, name: "Clarabelle Anderson", avatar: "9.jpeg" },
    ];

    const edges = [
      { source: 0, target: 4, value: 1 },
      { source: 4, target: 5, value: 1 },
      { source: 4, target: 6, value: 1 },
      { source: 4, target: 7, value: 1 },
      { source: 1, target: 6, value: 1 },
      { source: 2, target: 5, value: 1 },
      { source: 3, target: 7, value: 1 },
      { source: 5, target: 6, value: 1 },
      { source: 6, target: 7, value: 1 },
      { source: 6, target: 8, value: 1 },
    ];

    const colorScale = d3
      .scaleOrdinal()
      .domain(d3.range(nodes.length))
      .range(d3.schemeCategory10);

    const forceSimulation = d3
      .forceSimulation()
      .force("charge", d3.forceManyBody())
      .force("link", d3.forceLink())
      .force("center", d3.forceCenter())
      .force("collision", d3.forceCollide().radius(100));

    forceSimulation.nodes(nodes).on("tick", ticked);

    forceSimulation.force("link").links(edges).distance(100);

    forceSimulation
      .force("center")
      .x(width / 2)
      .y(height / 2);

    const links = g
      .append("g")
      .selectAll("line")
      .data(edges)
      .enter()
      .append("line")
      .attr("stroke", function (d, i) {
        return colorScale(i);
      })
      .attr("stroke-width", 1);

    const linksText = g
      .append("g")
      .selectAll("text")
      .data(edges)
      .enter()
      .append("text")
      .text(function (d) {
        return d.relation;
      });

    const gs = g
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

    gs.append("defs")
      .append("pattern")
      .attr("x", "0%")
      .attr("y", "0%")
      .attr("height", "100%")
      .attr("width", "100%")
      .attr("viewBox", "0 0 32 32")
      .attr("id", (d) => `avatar-${d.id}`)
      .attr("preserveAspectRatio", "xMidYMid slice")
      .append("image")
      .attr("width", 32)
      .attr("height", 32)
      .attr("preserveAspectRatio", "xMinYMin slice")
      .attr("href", (d) => `./assets/${d.avatar}`);

    gs.append("circle")
      .attr("stroke", "#fff")
      .attr("fill", "#ccc")
      .attr("fill", (d) => `url(#avatar-${d.id})`)
      .attr("r", 32);

    // Word
    gs.append("text")
      .attr("y", 50)
      .attr("alignment-baseline", "middle")
      .attr("text-anchor", "middle")
      .text(function (d) {
        return d.name;
      });

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
