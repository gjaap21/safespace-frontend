<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";
import ReportComponent from "./ReportComponent.vue";

const { isLoggedIn } = storeToRefs(useUserStore());

const loaded = ref(false);
let reports = ref<Array<Record<string, string>>>([]);

async function getReports() {
  let reportResults;
  try {
    reportResults = await fetchy("/api/reports", "GET");
  } catch (_) {
    return;
  }
  reports.value = reportResults;
}

onBeforeMount(async () => {
  await getReports();
  loaded.value = true;
});
</script>

<template>
  <div class="row">
    <h2>Reports:</h2>
  </div>
  <section class="reports" v-if="loaded && reports.length !== 0">
    <article v-for="report in reports" :key="report._id">
      <ReportComponent :report="report" @refreshReports="getReports" />
    </article>
  </section>
  <p v-else-if="loaded">No reports found</p>
  <p v-else>Loading...</p>
</template>

<style scoped>
section {
  display: flex;
  flex-direction: column;
  gap: 1em;
}

section,
p,
.row {
  margin: 0 auto;
  max-width: 60em;
}

article {
  background-color: var(--base-bg);
  border-radius: 1em;
  display: flex;
  flex-direction: column;
  gap: 0.5em;
  padding: 1em;
}

.reports {
  padding: 1em;
}

.row {
  display: flex;
  justify-content: space-between;
  margin: 0 auto;
  max-width: 60em;
}
</style>
