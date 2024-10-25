<script setup lang="ts">
import PostComponent from "@/components/Post/PostComponent.vue";
// import ReportForm from "@/components/Report/ReportForm.vue";
// import { fetchy } from "@/utils/fetchy";
import { ref } from "vue";

let post = ref<Record<string, string>>();
const props = defineProps(["userId"]);

const getPost = async () => {
  let postResult;
  try {
    // postResult = await fetchy(`/api/posts/${props.postId}`, "GET");
  } catch (_) {
    return;
  }
  post.value = postResult;
};
</script>

<template>
  <main>
    <h1>Report</h1>
    <div class="report-menu">
      <PostComponent :post="getPost" />
      <!-- <ReportForm :postId="props.postId" class="report-form" /> -->
    </div>
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
}

.report-menu {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.report-form {
  width: 40%;
}
</style>
