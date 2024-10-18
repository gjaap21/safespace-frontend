<script setup lang="ts">
import { formatDate } from "@/utils/formatDate";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["report"]);
const emit = defineEmits(["refreshReports"]);

async function deleteReport(validity: string) {
  try {
    await fetchy(`/api/reports/${props.report._id}`, "DELETE", { body: { validity } });
  } catch {
    return;
  }
  emit("refreshReports");
}
</script>

<template>
  <p>{{ props.report.item }}</p>
  <p>{{ props.report.info }}</p>
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
</style>
