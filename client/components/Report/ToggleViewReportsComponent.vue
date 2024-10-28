<script setup lang="ts">
import { ref } from "vue";
import PostListComponent from "../Post/PostListComponent.vue";
import ReportListComponent from "./ReportListComponent.vue";

let currView = ref("posts");

async function setView(view: string) {
  currView.value = view;
}
</script>

<template>
  <div>
    <div class="header">
      <h2 class="header-item" :class="{ active: currView === 'posts' }" @click="setView('posts')">Posts</h2>
      <h2 class="header-item" :class="{ active: currView === 'reports' }" @click="setView('reports')">Reports</h2>
    </div>

    <div class="content">
      <div v-if="currView === 'posts'">
        <PostListComponent />
      </div>
      <div v-else>
        <ReportListComponent />
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
