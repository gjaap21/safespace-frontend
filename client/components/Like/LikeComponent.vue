<script setup lang="ts">
import { onBeforeMount, ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["item"]);
const emit = defineEmits(["refreshLikes"]);
let liked = ref(false);
let likes = ref(0);

const toggleLike = async () => {
  if (liked.value) {
    try {
      await fetchy(`/api/likes/${props.item._id}`, "DELETE");
      liked.value = false;
    } catch {
      return;
    }
  } else {
    try {
      await fetchy(`/api/likes`, "POST", { body: { item: props.item._id } });
      liked.value = true;
    } catch {
      return;
    }
  }
  await getLikes();
};

const getLikes = async () => {
  try {
    likes.value = await fetchy(`/api/likes/items/${props.item._id}`, "GET");
  } catch {
    return;
  }
};

onBeforeMount(async () => {
  await getLikes();
});
</script>

<template>
  <div class="likes">
    <div @click="toggleLike" class="like-button">
      <svg
        v-if="!liked"
        xmlns="http://www.w3.org/2000/svg"
        width="24"
        height="24"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z" />
      </svg>
      <svg v-else xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="red">
        <path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z" />
      </svg>
    </div>
    <p class="like-count">{{ likes }}</p>
  </div>
</template>

<style scoped>
.like-button {
  cursor: pointer;
}

.likes {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.like-count {
  margin-left: 7px;
}
</style>
