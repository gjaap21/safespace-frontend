<script setup lang="ts">
import { computed, ref } from "vue";

const props = defineProps(["post"]);
const blurIntensity = ref(0);

const imageStyle = computed(() => {
  return {
    filter: `blur(${blurIntensity.value}px)`,
  };
});
</script>

<template>
  <div class="imageContainer">
    <iframe :src="props.post.image" :style="imageStyle" width="500" height="500" allow="autoplay"></iframe>
    <p>{{ props.post.caption }}</p>
    <div class="sliderContainer">
      <label for="blurRange">Blur: {{ blurIntensity }}</label>
      <input id="blurRange" type="range" min="0" max="50" v-model="blurIntensity" class="slider" />
    </div>
  </div>
</template>

<style scoped>
p {
  margin: 0em;
}

.author {
  font-weight: bold;
  font-size: 1.2em;
}

.timestamp {
  display: flex;
  justify-content: flex-end;
  font-size: 0.9em;
  font-style: italic;
}

.imageContainer {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 2px;
  width: 500px;
}

.sliderContainer {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  width: 40%;
}
</style>
