<template>
  <div class="gameObject"/>
</template>

<style scoped>
.gameObject {
  position: absolute;

  left: v-bind("positionCss.left");
  bottom: v-bind("positionCss.top");

  height: v-bind("positionCss.height");
  width: v-bind("positionCss.width");

  border-radius: 100%;

  background-repeat: no-repeat;
  background-size: contain;
  background-position: center;

  transform: translate(-50%, 50%);
  z-index: 20;
  /* rotate(v-bind("positionCss.rotation")); */
}
</style>

<script setup>
const props = defineProps(["data", "resolution"])

console.log(`${props.data.type} Point(${props.data.center.x}, ${props.data.center.y})`);

const positionCss = computed(() => ({
  left: `${props.data.center.x * props.resolution}rem`,
  top: `${props.data.center.y * props.resolution}rem`,

  height: `${2*props.data.radius * props.resolution}rem`,
  width: `${2*props.data.radius * props.resolution}rem`,

  rotation: `${props.data.rotation}deg`,
}));
</script>
