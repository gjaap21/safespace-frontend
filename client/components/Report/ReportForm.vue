<script setup lang="ts">
import router from "@/router";
import { ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["postId"]);
const violations = [
  { label: "Violence/Hate" },
  { label: "Bullying" },
  { label: "Suicide/Self-Injury" },
  { label: "Nudity/Explicit" },
  { label: "Scam/Fraud" },
  { label: "False Information" },
  { label: "Spam" },
];
const additionalInfo = ref("");
const selectedViolations = ref([]);

const submitReport = async () => {
  const info = additionalInfo.value + (selectedViolations.value.length ? `\nViolations: ${selectedViolations.value.join(", ")}` : "");
  await fetchy("/api/reports", "POST", { body: { id: props.postId, info } });
  void router.push({ path: `/` });
};

const cancelReport = async () => {
  void router.push({ path: `/` });
};
</script>

<template>
  <div class="report-form">
    <h2>Report Form</h2>

    <button class="cancel-button" @click="cancelReport">x</button>

    <form @submit.prevent="submitReport">
      <div class="form-group">
        <h3>Guideline Violations:</h3>
        <div v-for="violation in violations" :key="violation.label">
          <input type="checkbox" :id="violation.label" :value="violation.label" v-model="selectedViolations" />
          <label :for="violation.label">{{ violation.label }}</label>
        </div>
      </div>

      <div class="form-group">
        <label for="additionalInfo">If you'd like, you can tell us more about the issue:</label>
        <textarea id="additionalInfo" v-model="additionalInfo" placeholder="Enter any additional details here..."></textarea>
      </div>

      <button type="submit" class="submit-button">Submit</button>
    </form>
  </div>
</template>

<style scoped>
form {
  background-color: var(--base-bg);
  display: flex;
  flex-direction: column;
  gap: 0.5em;
}

label {
  display: block;
  margin-bottom: 5px;
}

textarea {
  font-family: inherit;
  font-size: inherit;
  height: 6em;
  border-radius: 4px;
  resize: none;
}

p {
  margin: 0em;
}

.author {
  font-weight: bold;
  font-size: 1.2em;
}

menu {
  list-style-type: none;
  display: flex;
  flex-direction: row;
  gap: 1em;
  padding: 0;
  margin: 0;
}

.base {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.timestamp {
  display: flex;
  justify-content: flex-end;
  font-size: 0.9em;
  font-style: italic;
}
</style>
