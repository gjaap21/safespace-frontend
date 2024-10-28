<script setup lang="ts">
import router from "@/router";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";
import { fetchy } from "../../utils/fetchy";
import ProfilePicComponent from "./ProfilePicComponent.vue";

const props = defineProps(["username"]);
const loaded = ref(false);
const { currentUsername } = storeToRefs(useUserStore());
let badges = ref<Array<Record<string, string>>>([]);

async function toHome() {
  void router.push({ path: `/` });
}

async function toSettings() {
  void router.push({ path: `/setting` });
}

const getBadges = async () => {
  let badgeResults;
  try {
    badgeResults = await fetchy("/api/badges", "GET", { query: { user: props.username } });
  } catch (_) {
    return;
  }
  badges.value = badgeResults;
};

onBeforeMount(async () => {
  await getBadges();
  loaded.value = true;
});
</script>

<template>
  <div class="container">
    <div class="profile">
      <ProfilePicComponent :username="props.username" />
      <p class="author">{{ props.username }}</p>
    </div>
    <div v-if="loaded && badges.length !== 0" class="badges">
      <article v-for="badge in badges" :key="badge._id">
        <div v-if="badge.type == 'shame'">
          <img src="@/assets/images/shame.svg" class="badge" />
        </div>
      </article>
    </div>
    <div class="options">
      <div class="homebutton" @click="toHome()">
        <svg xmlns="http://www.w3.org/2000/svg" width="50" height="50" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1" stroke-linecap="round" stroke-linejoin="round">
          <path d="M3 9l9-6 9 6v12a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V9z" />
          <path d="M9 22V12h6v10" />
        </svg>
      </div>
      <div v-if="props.username == currentUsername" @click="toSettings()">
        <img src="@/assets/images/settings.svg" class="settings" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.author {
  font-weight: bold;
  font-size: 1.4em;
  margin-top: 8px;
}

.profile {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-left: 15px;
  margin-top: 15px;
}

.container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  border-bottom: 2px solid #000;
}

.options {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  justify-content: space-evenly;
  margin-right: 15px;
}

.settings {
  cursor: pointer;
  width: 50px;
  height: 50px;
}

.homebutton {
  cursor: pointer;
}

.badges {
  display: flex;
  align-items: center;
}

.badge {
  width: 100px;
  height: 100px;
}
</style>
