<script setup lang="ts">
import { fetchy } from "@/utils/fetchy";
import { ref } from "vue";

let newUser = ref("");
let oldUser = ref("");
let filters = ref<Array<Record<string, string>>>([]);

async function addFilter() {
  try {
    await fetchy("/api/filters", "POST", {
      body: { filterUser: newUser.value },
    });
  } catch (_) {
    return;
  }
  await getFilters();
}

async function removeFilter() {
  try {
    await fetchy(`/api/filters/${oldUser.value}`, "DELETE");
  } catch {
    return;
  }
  await getFilters();
}

async function getFilters() {
  let filterResults;
  try {
    filterResults = await fetchy(`/api/filters`, "GET");
  } catch (_) {
    return;
  }
  filters.value = filterResults;
}
</script>

<template>
  <h2>Update filters</h2>
  <p>Add a user to blur any of their future posts that you come across!</p>
  <form @submit.prevent="addFilter()" class="pure-form">
    <fieldset>
      <legend>Add a user</legend>
      <input type="text" placeholder="User" v-model="newUser" required />
      <button type="submit" class="pure-button pure-button-primary">Add user</button>
    </fieldset>
  </form>

  <form @submit.prevent="removeFilter" class="pure-form">
    <fieldset>
      <legend>Remove a user</legend>
      <input type="text" placeholder="User" v-model="oldUser" required />
      <button type="submit" class="pure-button pure-button-primary">Remove user</button>
    </fieldset>
  </form>

  <button @click="getFilters" class="filters-button">See all filtered users</button>
  <article v-for="filter in filters" :key="filter._id">
    <p>{{ filter }}</p>
  </article>
</template>

<style>
.filters-button {
  padding: 10px 20px;
  margin-bottom: 20px;
  background-color: #007bff; /* Bootstrap primary color */
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

.filters-button:hover {
  background-color: #0056b3; /* Darker shade for hover effect */
}
</style>
