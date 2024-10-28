<script setup lang="ts">
import router from "@/router";
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";
import CommentComponent from "./CommentComponent.vue";

const { isLoggedIn } = storeToRefs(useUserStore());

const props = defineProps(["postId"]);
const loaded = ref(false);
let comments = ref<Array<Record<string, string>>>([]);
const newComment = ref("");

async function getComments(item: string) {
  let query: Record<string, string> = item !== undefined ? { item } : {};
  let commentResults;
  try {
    commentResults = await fetchy("/api/comments", "GET", { query });
  } catch (_) {
    return;
  }
  comments.value = commentResults;
}

const addComment = async () => {
  if (newComment.value.trim()) {
    await fetchy("/api/comments", "POST", { body: { item: props.postId, content: newComment.value } });
    newComment.value = "";
  }
  await getComments(props.postId);
};

async function reportComment(postId: string) {
  void router.push({ path: `/report/${postId}` });
}

onBeforeMount(async () => {
  await getComments(props.postId);
  loaded.value = true;
});
</script>

<template>
  <div class="comment-box">
    <section class="comments" v-if="loaded && comments.length !== 0">
      <article v-for="comment in comments" :key="comment._id">
        <CommentComponent :comment="comment" @refreshPosts="getComments" class="comment" />
        <div class="comment-interaction">
          <button class="button-error btn-small pure-button" @click="reportComment(comment._id)">Report</button>
        </div>
      </article>
    </section>
    <p v-else-if="loaded">No comments found</p>
    <p v-else>Loading...</p>
    <section v-if="isLoggedIn" class="input-area">
      <input type="text" v-model="newComment" @keyup.enter="addComment" placeholder="..." />
      <button @click="addComment">Submit</button>
    </section>
  </div>
</template>

<style scoped>
.comment-box {
  border: 1px;
  border-radius: 8px;
  overflow: hidden;
  max-width: 400px;
}

.comments {
  max-height: 200px;
  overflow-y: auto;
  padding: 10px;
  background-color: #f9f9f9;
}

.comment {
  margin-bottom: 8px;
}

.input-area {
  display: flex;
  padding: 10px;
  background-color: #fff;
}

input[type="text"] {
  flex: 1;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-right: 10px;
}

button {
  padding: 8px 12px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

.comment-interaction {
  display: flex;
  align-items: center;
  justify-content: flex-end;
}
</style>
