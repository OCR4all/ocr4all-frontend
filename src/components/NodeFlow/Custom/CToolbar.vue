<script setup lang="ts">
import { Commands } from "baklavajs";
import { useViewModel } from "baklavajs";
import { useI18n } from "vue-i18n";

const { t } = useI18n();

const { viewModel } = useViewModel();

const emit = defineEmits([
  "new",
  "load",
  "save",
  "zoom-in",
  "zoom-out",
  "rate-workflow",
  "toggle-palette",
]);
</script>

<template>
  <div
    class="baklava-toolbar !z-10 !rounded-none !border-b-2 !border-solid !border-surface-200 !bg-surface-50 dark:!border-zinc-950 dark:!bg-zinc-900"
  >
    <div class="flex justify-between">
      <div class="flex space-x-4">
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.undo')">
          <i
            class="pi pi-undo self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="
              viewModel.commandHandler.executeCommand(Commands.UNDO_COMMAND)
            "
          ></i>
        </span>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.redo')">
          <i
            class="pi pi-undo -scale-x-100 self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="
              viewModel.commandHandler.executeCommand(Commands.REDO_COMMAND)
            "
          ></i>
        </span>
        <div
          class="inline-block w-0.5 self-stretch bg-surface-500 opacity-100 dark:bg-surface-400 dark:opacity-50"
        ></div>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.new-workflow')">
          <i
            class="pi pi-plus self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="emit('new')"
          ></i>
        </span>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.load-workflow')">
          <i
            class="pi pi-upload self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="emit('load')"
          ></i>
        </span>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.save-workflow')">
          <i
            class="pi pi-save self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="emit('save')"
          ></i>
        </span>
        <div
          class="inline-block w-0.5 self-stretch bg-surface-500 opacity-100 dark:bg-surface-400 dark:opacity-50"
        ></div>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.select')">
          <i
            class="pi pi-copy self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="
              viewModel.commandHandler.executeCommand(
                Commands.START_SELECTION_BOX_COMMAND,
              )
            "
          ></i>
        </span>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.copy')">
          <i
            class="pi pi-copy self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="
              viewModel.commandHandler.executeCommand(Commands.COPY_COMMAND)
            "
          ></i>
        </span>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.paste')">
          <i
            class="pi pi-search-plus self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="
              viewModel.commandHandler.executeCommand(Commands.PASTE_COMMAND)
            "
          ></i>
        </span>
        <div
          class="inline-block w-0.5 self-stretch bg-surface-500 opacity-100 dark:bg-surface-400 dark:opacity-50"
        ></div>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.zoom-out')">
          <i
            class="pi pi-search-minus self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="emit('zoom-out')"
          ></i>
        </span>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.zoom-in')">
          <i
            class="pi pi-search-plus self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="emit('zoom-in')"
          ></i>
        </span>
        <div
          class="inline-block w-0.5 self-stretch bg-surface-500 opacity-100 dark:bg-surface-400 dark:opacity-50"
        ></div>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.rate-workflow')">
          <i
            class="pi pi-check-circle self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="emit('rate-workflow')"
          ></i>
        </span>
        <div
          class="inline-block w-0.5 self-stretch bg-surface-500 opacity-100 dark:bg-surface-400 dark:opacity-50"
        ></div>
        <span v-tooltip="t('pages.nodeflow.toolbar.tooltips.toggle-palette')">
          <i
            class="pi pi-align-right self-center text-xl text-surface-600 hover:cursor-pointer hover:text-black dark:text-surface-400 dark:hover:text-white"
            @click="emit('toggle-palette')"
          ></i>
        </span>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
