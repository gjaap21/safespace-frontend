<script setup lang="ts">
import { formatDate } from "@/utils/formatDate";
import { onBeforeMount, ref } from "vue";
import { fetchy } from "../../utils/fetchy";
import BasicPostComponent from "../Post/BasicPostComponent.vue";

const props = defineProps(["report"]);
console.log(props.report);
const emit = defineEmits(["refreshReports", "refreshPosts"]);
let post = ref<Record<string, string>>();
let loaded = ref(false);

const getPost = async () => {
  let postResult;
  try {
    postResult = await fetchy(`/api/posts/${props.report.item}`, "GET");
  } catch (_) {
    return;
  }
  post.value = postResult;
  console.log(postResult);
};

onBeforeMount(async () => {
  await getPost();
  loaded.value = true;
});

async function deleteReport(validity: string) {
  try {
    await fetchy(`/api/reports/${validity}/${props.report._id}`, "DELETE");
  } catch {
    return;
  }
  emit("refreshReports");
  emit("refreshPosts");
}
</script>

<template>
  <div v-if="loaded" class="report-menu">
    <BasicPostComponent :post="post" />
    <p class="report-info">{{ props.report.info }}</p>
  </div>
  <div class="base">
    <button class="btn-small pure-button" @click="deleteReport('true')">Validate</button>
    <button class="button-error btn-small pure-button" @click="deleteReport('false')">Invalidate</button>
    <article class="timestamp">
      <p v-if="props.report.dateCreated !== props.report.dateUpdated">Edited on: {{ formatDate(props.report.dateUpdated) }}</p>
      <p v-else>Created on: {{ formatDate(props.report.dateCreated) }}</p>
    </article>
  </div>
</template>

<style scoped>
p {
  margin: 0em;
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

.report-menu {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.report-info {
  font-size: 1.2em;
  white-space: normal;
  overflow-wrap: break-word;
}
</style>
