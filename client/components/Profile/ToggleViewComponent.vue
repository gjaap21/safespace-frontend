<script setup lang="ts">
import { ref } from "vue";
import ProfilePostsComponent from "./ProfilePostsComponent.vue";

const props = defineProps(["username"]);
let currView = ref("posts");

async function setView(view: string) {
  currView.value = view;
}
</script>

<template>
  <div>
    <div class="header">
      <h2 class="header-item" :class="{ active: currView === 'posts' }" @click="setView('posts')">Posts</h2>
      <h2 class="header-item" :class="{ active: currView === 'likes' }" @click="setView('likes')">Likes</h2>
    </div>

    <div class="content">
      <div v-if="currView === 'posts'">
        <ProfilePostsComponent :username="props.username" />
      </div>
      <div v-else>
        <p>User's likes will be displayed here.</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.header {
  display: flex;
  justify-content: space-around;
  margin-bottom: 20px;
}

.header-item {
  cursor: pointer;
  padding: 10px;
  transition: color 0.3s;
}

.header-item.active {
  color: blue;
  font-weight: bold;
}

.content {
  /* Add styles for content area */
}
</style>
