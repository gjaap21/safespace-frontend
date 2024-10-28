<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { formatDate } from "@/utils/formatDate";
import { storeToRefs } from "pinia";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["comment"]);
const emit = defineEmits(["refreshComments"]);
const { currentUsername, isAdmin } = storeToRefs(useUserStore());

const deleteComment = async () => {
  try {
    if (isAdmin) {
      await fetchy(`/api/admins/${props.comment._id}`, "DELETE");
    } else await fetchy(`/api/comments/${props.comment._id}`, "DELETE");
  } catch {
    return;
  }
  emit("refreshComments");
};
</script>

<template>
  <div class="textbox">
    <p class="author">{{ props.comment.author }}:</p>
    <p>{{ props.comment.content }}</p>
  </div>
  <div class="base">
    <menu v-if="props.comment.author == currentUsername || isAdmin">
      <li><button class="button-error btn-small pure-button" @click="deleteComment">Delete</button></li>
    </menu>
    <article class="timestamp">
      <p v-if="props.comment.dateCreated !== props.comment.dateUpdated">Edited on: {{ formatDate(props.comment.dateUpdated) }}</p>
      <p v-else>Created on: {{ formatDate(props.comment.dateCreated) }}</p>
    </article>
  </div>
</template>

<style scoped>
p {
  margin: 0em;
}

.author {
  font-weight: bold;
  font-size: 1em;
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

.textbox {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}
</style>
