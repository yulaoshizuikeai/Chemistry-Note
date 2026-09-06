<script setup>
import { pageviewCount } from "@waline/client";
import { onBeforeUnmount, onMounted, watch } from "vue";

import { useWalineBase } from "./useWalineBase";

const { serverURL, route } = useWalineBase();
let abortPageview = null;

const runPageview = (path) => {
  if (abortPageview) abortPageview();
  abortPageview = pageviewCount({ serverURL, path });
};

onMounted(() => {
  runPageview(route.path);
});

watch(
  () => route.path,
  (path) => {
    if (typeof window === "undefined") return;
    runPageview(path);
  },
);

onBeforeUnmount(() => {
  if (abortPageview) abortPageview();
});
</script>

<template>
  <span class="waline-pageview">
    <svg
      class="waline-pageview-icon"
      viewBox="0 0 24 24"
      width="14"
      height="14"
      fill="none"
      stroke="currentColor"
      stroke-width="2"
      stroke-linecap="round"
      stroke-linejoin="round"
      aria-hidden="true"
    >
      <path d="M2 12s3-7 10-7 10 7 10 7-3 7-10 7-10-7-10-7Z" />
      <circle cx="12" cy="12" r="3" />
    </svg>
    <span class="waline-pageview-count"></span> views
  </span>
</template>

<style scoped>
.waline-pageview {
  display: inline-flex;
  gap: 0.35em;
  align-items: center;
  white-space: nowrap;
  font-size: 14px;
}

.waline-pageview-icon {
  flex-shrink: 0;
  opacity: 0.75;
}
</style>
