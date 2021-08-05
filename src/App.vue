<template>
  <div>
    <svg @click="addPoint" class="w-full h-screen bg-gray-200">
      <defs>
        <pattern
          id="image"
          x="0%"
          y="0%"
          height="100%"
          width="100%"
          viewBox="0 0 512 512"
        >
          <image
            x="0%"
            y="0%"
            width="512"
            height="512"
            href="./assets/photo.jpeg"
          ></image>
        </pattern>
      </defs>
      <path
        fill="transparent"
        stroke="#000"
        stroke-width="1"
        :d="lineGenerator(points)"
      />
      <circle
        v-for="(item, index) in points"
        :key="index"
        :cx="item.x"
        :cy="item.y"
        r="20"
        fill="url(#image)"
      />
    </svg>
  </div>
</template>

<script>
import { line, curveMonotoneX } from "d3";
export default {
  data() {
    return {
      points: [
        {
          x: 100,
          y: 100,
        },
      ],
    };
  },
  methods: {
    addPoint(event) {
      const { layerX: x, layerY: y } = event;

      this.points.push({ x, y });
    },
  },
  computed: {
    lineGenerator() {
      return line()
        .x((item) => item.x)
        .y((item) => item.y)
        .curve(curveMonotoneX);
    },
  },
};
</script>
