<script setup lang="ts">
import BasicPostComponent from "@/components/Post/BasicPostComponent.vue";
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";

const { isLoggedIn } = storeToRefs(useUserStore());

const props = defineProps(["username"]);
const loaded = ref(false);
let posts = ref<Array<Record<string, string>>>([]);

async function getPosts(author: string) {
  let query: Record<string, string> = author !== undefined ? { author } : {};
  let postResults;
  try {
    postResults = await fetchy("/api/posts", "GET", { query });
  } catch (_) {
    return;
  }
  posts.value = postResults;
}

onBeforeMount(async () => {
  await getPosts(props.username);
  loaded.value = true;
});
</script>

<template>
  <section class="posts" v-if="loaded && posts.length !== 0">
    <article v-for="post in posts" :key="post._id">
      <BasicPostComponent :post="post" />
    </article>
  </section>
  <p v-else-if="loaded">No posts yet. Try making a fun new post!</p>
  <p v-else>Loading...</p>
</template>

<style scoped>
section {
  display: flex;
  gap: 1em;
}

article {
  background-color: var(--base-bg);
  border-radius: 1em;
  display: flex;
  gap: 0.5em;
  padding: 1em;
}

.posts {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
  justify-content: flex-start;
}
</style>
