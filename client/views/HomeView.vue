<script setup lang="ts">
import PostListComponent from "@/components/Post/PostListComponent.vue";
import ToggleViewReportsComponent from "@/components/Report/ToggleViewReportsComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const { currentUsername, isLoggedIn, isAdmin } = storeToRefs(useUserStore());
</script>

<template>
  <main>
    <h1>Home Page</h1>
    <section>
      <h1 v-if="isLoggedIn">Welcome {{ currentUsername }}!</h1>
      <h1 v-else>Please login!</h1>
    </section>
    <div v-if="isAdmin">
      <ToggleViewReportsComponent />
    </div>
    <div v-else>
      <PostListComponent />
    </div>
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
}
</style>
