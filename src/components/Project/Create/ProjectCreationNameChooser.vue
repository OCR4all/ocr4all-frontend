<script setup lang="ts">
import InputText from "primevue/inputtext";
import InlineMessage from "primevue/inlinemessage";

import { useProjectCreationStore } from "@/stores/projectCreation.store";

import { useCustomFetch } from "@/composables/useCustomFetch";

const nameTaken = ref(false);
const noName = ref(false);
const projects = ref([]);

const emit = defineEmits(["back", "next"]);

const projectName = ref();

const store = useProjectCreationStore();

useCustomFetch(`/project/list`)
  .json()
  .then((response) => {
    projects.value = [...response.data.value.map((entry) => entry.id)];
  });

async function createProject() {
  if (!projectName.value) {
    nameTaken.value = false;
    noName.value = true;
  } else {
    noName.value = false;

    if (projects.value.includes(projectName.value)) {
      nameTaken.value = true;
    } else {
      useCustomFetch(`/project/create?id=${projectName.value}`)
        .get()
        .json()
        .then((response) => {
          if (!response.error.value) {
            store.projectId = projectName.value;
            emit("next");
          }
        });
    }
  }
}
</script>

<template>
  <div
    class="flex flex-col items-center justify-center space-y-10 dark:text-surface-100 sm:p-12"
  >
    <h2
      class="mb-2 text-xl font-bold text-black dark:text-white sm:text-2xl md:text-3xl"
    >
      {{ $t("pages.projects.new.components.name.directive") }}
    </h2>
    <div class="flex flex-col space-y-2">
      <InputText
        v-model="projectName"
        :class="{ 'border border-solid border-red-700': nameTaken === true }"
      />
      <InlineMessage v-show="noName">{{
        $t("pages.projects.new.components.name.warning.no-name")
      }}</InlineMessage>
      <InlineMessage v-show="nameTaken">{{
        $t("pages.projects.new.components.name.warning.name-taken")
      }}</InlineMessage>
    </div>
    <button
      type="button"
      class="mb-2 mr-2 rounded-lg bg-primary-700 px-5 py-2.5 text-sm font-medium text-white hover:bg-primary-800 focus:outline-none focus:ring-4 focus:ring-primary-300 dark:bg-primary-600 dark:hover:bg-primary-700 dark:focus:ring-primary-800"
      @click="createProject"
    >
      {{ $t("pages.projects.new.components.name.buttons.create") }}
    </button>
  </div>
</template>
