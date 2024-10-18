<script setup lang="ts">
import { ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const image = ref("");
const caption = ref("");
const emit = defineEmits(["refreshPosts"]);

const createPost = async (image: string, caption: string) => {
  try {
    await fetchy("/api/posts", "POST", {
      body: { image, caption },
    });
  } catch (_) {
    return;
  }
  emit("refreshPosts");
  emptyImageForm();
  emptyCaptionForm();
};

const emptyImageForm = () => {
  image.value = "";
};
const emptyCaptionForm = () => {
  caption.value = "";
};
</script>

<template>
  <form @submit.prevent="createPost(image, caption)">
    <label for="content">Post Contents:</label>
    <textarea id="image" v-model="image" placeholder="Insert a cool image!" required> </textarea>
    <textarea id="caption" v-model="caption" placeholder="Add a fun caption!" required> </textarea>
    <button type="submit" class="pure-button-primary pure-button">Create Post</button>
  </form>
</template>

<style scoped>
form {
  background-color: var(--base-bg);
  border-radius: 1em;
  display: flex;
  flex-direction: column;
  gap: 0.5em;
  padding: 1em;
}

textarea {
  font-family: inherit;
  font-size: inherit;
  height: 5em;
  padding: 0.5em;
  border-radius: 4px;
  resize: none;
}
</style>
