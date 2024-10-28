<script setup lang="ts">
import BasicPostComponent from "@/components/Post/BasicPostComponent.vue";
import ReportForm from "@/components/Report/ReportForm.vue";
import { fetchy } from "@/utils/fetchy";
import { onBeforeMount, ref } from "vue";

const props = defineProps(["postId"]);
let post = ref<Record<string, string>>();
let author = "";

const getPost = async () => {
  let postResult;
  try {
    postResult = await fetchy(`/api/posts/${props.postId}`, "GET");
  } catch (_) {
    return;
  }
  post.value = postResult;
  author = postResult.author;
};

onBeforeMount(async () => {
  await getPost();
});
</script>

<template>
  <main>
    <h1>Report</h1>
    <div class="report-menu">
      <div class="post-comp">
        <p class="author">{{ author }}</p>
        <BasicPostComponent :post="post" />
      </div>
      <ReportForm :postId="props.postId" class="report-form" />
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

.author {
  font-weight: bold;
  font-size: 1.2em;
  margin-bottom: 3px;
}

.post-comp {
  display: flex;
  flex-direction: column;
}
</style>
