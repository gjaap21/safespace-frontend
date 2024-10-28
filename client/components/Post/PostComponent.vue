<script setup lang="ts">
import router from "@/router";
import { useUserStore } from "@/stores/user";
import { formatDate } from "@/utils/formatDate";
import { storeToRefs } from "pinia";
import { fetchy } from "../../utils/fetchy";
import CommentListComponent from "../Comment/CommentListComponent.vue";
import LikeComponent from "../Like/LikeComponent.vue";
import BasicPostComponent from "./BasicPostComponent.vue";

const props = defineProps(["post"]);
const emit = defineEmits(["editPost", "refreshPosts"]);
const { currentUsername, isAdmin } = storeToRefs(useUserStore());

const deletePost = async () => {
  try {
    if (isAdmin) {
      await fetchy(`/api/admins/${props.post._id}`, "DELETE");
    } else await fetchy(`/api/posts/${props.post._id}`, "DELETE");
  } catch {
    return;
  }
  emit("refreshPosts");
};

async function toProfile(username: string) {
  void router.push({ path: `/profile/${username}` });
}
</script>

<template>
  <p class="author" @click="toProfile(props.post.author)">{{ props.post.author }}</p>
  <div class="main">
    <BasicPostComponent :post="props.post" />
    <div class="post-actions">
      <CommentListComponent :postId="props.post._id" />
      <div class="likes">
        <LikeComponent :item="props.post" />
      </div>
    </div>
  </div>
  <div class="base">
    <menu v-if="props.post.author == currentUsername || isAdmin">
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
  cursor: pointer;
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

.main {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}

.post-actions {
  display: flex;
  flex-direction: column;
  margin-left: 10px;
}
</style>
