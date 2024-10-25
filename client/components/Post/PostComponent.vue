<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { formatDate } from "@/utils/formatDate";
import { storeToRefs } from "pinia";
import { onMounted, ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["post"]);
const emit = defineEmits(["editPost", "refreshPosts"]);
const { currentUsername } = storeToRefs(useUserStore());

const postImage = ref<HTMLCanvasElement | null>(null);

// const displayPost = async () => {
//   const image = await fetchy("/api/blur", "POST", { body: { id: props.post._id } });
//   postImage.value!.appendChild(image);
// };
const displayPost = async () => {
  // console.log("hi");
  if (!postImage.value) return;
  // console.log("yo");
  const context = postImage.value.getContext("2d");
  if (!context) return;

  // context.fillStyle = "lightblue"; // Background color
  // context.fillRect(0, 0, postImage.value.width, postImage.value.height);

  // // Draw a rectangle
  // context.fillStyle = "orange"; // Rectangle color
  // context.fillRect(50, 50, 200, 100); // (x, y, width, height)

  const img = new Image();
  img.src = props.post.image;
  img.crossOrigin = "Anonymous";

  img.onerror = () => {
    console.error("Failed to load image:", props.post.image);
  };

  img.onload = () => {
    postImage.value!.width = img.width;
    postImage.value!.height = img.height;

    // context.filter = `blur(${props.blurIntensity}px)`;
    context!.drawImage(img, 0, 0);
  };
};

onMounted(async () => {
  await displayPost();
});

const deletePost = async () => {
  try {
    await fetchy(`/api/posts/${props.post._id}`, "DELETE");
  } catch {
    return;
  }
  emit("refreshPosts");
};
</script>

<template>
  <p class="author">{{ props.post.author }}</p>
  <p>{{ props.post.image }}</p>
  <!-- <div ref="postImage"></div> -->
  <div>
    <canvas ref="postImage" width="500" height="500"></canvas>
  </div>
  <p>{{ props.post.caption }}</p>
  <div class="base">
    <menu v-if="props.post.author == currentUsername">
      <li><button class="btn-small pure-button" @click="emit('editPost', props.post._id)">Edit</button></li>
      <li><button class="button-error btn-small pure-button" @click="deletePost">Delete</button></li>
    </menu>
    <article class="timestamp">
      <p v-if="props.post.dateCreated !== props.post.dateUpdated">Edited on: {{ formatDate(props.post.dateUpdated) }}</p>
      <p v-else>Created on: {{ formatDate(props.post.dateCreated) }}</p>
    </article>
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

menu {
  list-style-type: none;
  display: flex;
  flex-direction: row;
  gap: 1em;
  padding: 0;
  margin: 0;
}

.timestamp {
  display: flex;
  justify-content: flex-end;
  font-size: 0.9em;
  font-style: italic;
}

.base {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.base article:only-child {
  margin-left: auto;
}

canvas {
  border: 1px solid #ccc; /* Optional: for better visibility */
}
</style>
