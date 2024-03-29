<script setup>
import { UseTimeAgo } from "@vueuse/components";
import {
  ArrowPathIcon,
  ArchiveBoxXMarkIcon,
  StopIcon,
  XMarkIcon,
} from "@heroicons/vue/24/outline";

import DataTable from "primevue/datatable";
import Column from "primevue/column";
import Tag from "primevue/tag";
import InputText from "primevue/inputtext";
import Dropdown from "primevue/dropdown";
import Toast from "primevue/toast";
import ProgressBar from "primevue/progressbar";
import { FilterMatchMode } from "primevue/api";

import { useI18n } from "vue-i18n";
const { t } = useI18n();

import { useToast } from "primevue/usetoast";
import { useCustomFetch } from "@/composables/useCustomFetch";
const toast = useToast();

const loading = ref(true);
const isRefetching = ref(false);

const filters = ref({
  global: { value: null, matchMode: FilterMatchMode.CONTAINS },
  state: { value: null, matchMode: FilterMatchMode.MATCHES },
});

const states = ref([
  "scheduled",
  "running",
  "interrupted",
  "cancelled",
  "completed",
]);
const jobs = ref();

const getColor = (entry) => {
  switch (entry) {
    case "scheduled":
      return { background: "#76A9FA" };
    case "running":
      return { background: "#1A56DB" };
    case "interrupted":
      return { background: "#4B5563" };
    case "cancelled":
      return { background: "#E02424" };
    case "completed":
      return { background: "#046C4E" };
    default:
      return null;
  }
};
async function refetch() {
  isRefetching.value = true;
  const { isFetching, error, data } = await useCustomFetch(
    `/job/overview/administration`,
  )
    .get()
    .json();
  const _jobs = [];
  for (const entries of Object.values(data.value)) {
    for (const job of entries) {
      _jobs.push(job);
    }
  }
  loading.value = isFetching.value;
  setTimeout(function () {
    isRefetching.value = isFetching.value;
  }, 500);
  jobs.value = _jobs;
}

refetch();

setInterval(function () {
  refetch();
}, 5000);
async function cancelJob(id) {
  const data = await useCustomFetch(`/job/cancel/${id}`).get().json();
  await refetch().then(() => {
    toast.add({
      severity: "success",
      summary: t("pages.queue.table.toasts.cancel.success.summary"),
      detail: t("pages.queue.table.toasts.cancel.success.detail"),
      life: 3000,
    });
  });
}

async function expungeJobs() {
  useCustomFetch(`job/scheduler/action/expunge`)
    .then(() => {
      refetch();
    })
    .then(() => {
      toast.add({
        severity: "success",
        summary: t("pages.queue.table.toasts.expunge.success.summary"),
        detail: t("pages.queue.table.toasts.expunge.success.detail"),
        life: 3000,
      });
    });
}

async function removeJob(job) {
  useCustomFetch(`job/remove/${job}`)
    .then(() => {
      refetch();
    })
    .then(() => {
      toast.add({
        severity: "success",
        summary: t("pages.queue.table.toasts.remove.success.summary"),
        detail: t("pages.queue.table.toasts.remove.success.detail"),
        life: 3000,
      });
    });
}
</script>

<template>
  <Toast />
  <div
    class="rounded-md bg-white shadow-md dark:border dark:border-surface-700 dark:bg-zinc-800"
  >
    <DataTable
      v-model:filters="filters"
      :value="jobs"
      paginator
      filter-display="row"
      :global-filter-fields="['id', 'description', 'state']"
      :loading="loading"
      :rows="5"
      :rows-per-page-options="[5, 10, 20, 50]"
      table-style="min-width: 50rem"
    >
      <template #empty>
        <span class="text-primary-950 dark:text-primary-50">{{
          $t("pages.queue.table.empty")
        }}</span>
      </template>
      <template #header>
        <div class="flex justify-between">
          <h2 class="my-4 text-xl">{{ $t("pages.queue.table.header") }}</h2>
          <span class="p-input-icon-left ml-10 space-x-4">
            <button
              v-tooltip="'Refresh'"
              :disabled="isRefetching === true"
              @click="refetch"
            >
              <ArrowPathIcon
                :class="{ 'animate-spin': isRefetching }"
                class="mr-2 inline h-6 w-6 text-surface-800 hover:text-black dark:text-surface-200 dark:hover:text-white"
              />
            </button>
            <button v-tooltip="'Expunge queue'" @click="expungeJobs">
              <ArchiveBoxXMarkIcon
                class="mr-2 inline h-6 w-6 text-surface-800 hover:text-red-600 dark:text-surface-200 dark:hover:text-red-600"
              />
            </button>
            <InputText
              v-model="filters['global'].value"
              :placeholder="$t('pages.queue.table.search.placeholder')"
            />
          </span>
        </div>
      </template>
      <Column :header="$t('pages.queue.table.columns.actions')">
        <template #body="slotProps">
          <span class="space-y-2">
            <button
              :disabled="
                !['running', 'scheduled'].includes(slotProps.data.state)
              "
              type="button"
              class="mr-2 inline-flex items-center rounded-md bg-red-700 p-2.5 text-center text-sm font-medium text-white hover:bg-red-800 focus:outline-none focus:ring-4 focus:ring-red-300 disabled:bg-red-200 dark:bg-red-600 dark:hover:bg-red-700 dark:focus:ring-red-800 dark:disabled:bg-red-400"
              @click="cancelJob(slotProps.data.id)"
            >
              <StopIcon class="h-6 w-6 text-white" />
            </button>
            <button
              :disabled="
                ['running', 'scheduled'].includes(slotProps.data.state)
              "
              type="button"
              class="mr-2 inline-flex items-center rounded-md bg-red-700 p-2.5 text-center text-sm font-medium text-white hover:bg-red-800 focus:outline-none focus:ring-4 focus:ring-red-300 disabled:bg-red-200 dark:bg-red-600 dark:hover:bg-red-700 dark:focus:ring-red-800 dark:disabled:bg-red-400"
              @click="removeJob(slotProps.data.id)"
            >
              <XMarkIcon class="h-6 w-6 text-white" />
            </button>
          </span>
        </template>
      </Column>
      <Column
        field="id"
        :sortable="true"
        :header="$t('pages.queue.table.columns.id')"
      ></Column>
      <Column
        field="description"
        :header="$t('pages.queue.table.columns.description')"
      ></Column>
      <Column
        field="state"
        :sortable="true"
        header="State"
        :show-filter-menu="false"
        :filter-menu-style="{ width: '14rem' }"
      >
        <template #body="{ data }">
          <Tag :value="data.state" :style="getColor(data.state)" />
        </template>
        <template #filter="{ filterModel, filterCallback }">
          <Dropdown
            v-model="filterModel.value"
            placeholder="Select State"
            :options="states"
            class="p-column-filter"
            :show-clear="true"
            @change="filterCallback()"
          >
            <template #option="slotProps">
              <Tag
                :value="slotProps.option"
                :style="getColor(slotProps.option)"
              />
            </template>
          </Dropdown>
        </template>
      </Column>
      <Column
        field="journal.progress"
        :header="$t('pages.queue.table.columns.progress')"
        sortable
        :show-filter-match-modes="false"
        style="min-width: 12rem"
      >
        <template #body="slotProps">
          <ProgressBar
            :value="slotProps.data.journal.progress * 100"
            :show-value="false"
            style="height: 6px"
          ></ProgressBar>
        </template>
      </Column>
      <Column
        :sortable="true"
        field="created"
        :header="$t('pages.queue.table.columns.created')"
      >
        <template #body="slotProps">
          <UseTimeAgo
            v-slot="{ timeAgo }"
            :time="Date.parse(slotProps.data.created)"
          >
            {{ timeAgo }}
          </UseTimeAgo>
        </template>
      </Column>
    </DataTable>
  </div>
</template>
