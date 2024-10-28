<script setup lang="ts">
import ProfileHeader from "@/components/Profile/ProfileHeader.vue";
import ToggleViewComponent from "@/components/Profile/ToggleViewComponent.vue";

const props = defineProps(["username"]);
</script>

<template>
  <main>
    <ProfileHeader :username="props.username" />
    <ToggleViewComponent :username="props.username" />
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
}
</style>
