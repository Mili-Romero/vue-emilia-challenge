<script setup lang="ts">
import { useFuse } from '@vueuse/integrations/useFuse'
import type { Note } from '~/types'
import { ApiProviderService } from "~/ApiProviderService";
import ResponseData from "@/types/ResponseData";

const apiService = new ApiProviderService();

const isFetching = ref(true);
const input = ref('')
const listNotes: Ref<Note[]> = ref([])

const getAllNotes = async() => {
  apiService.getAllNotes()
    .then((response: ResponseData) => {
      listNotes.value = response.data;
      isFetching.value = false;
      results.value = useFuse(input, listNotes, {
        fuseOptions: { keys: ['title', 'body', 'author'] },
        matchAllWhenSearchEmpty: true,
      })
    })
   .catch((e: Error) => {
      console.error(e);
      isFetching.value = false;
    });
}

const { results } = useFuse(input, listNotes, {
  fuseOptions: { keys: ['title', 'body', 'author'] },
  matchAllWhenSearchEmpty: true,
})

onMounted(() => {
  getAllNotes()
});
</script>

<template>
  <div class="grid gap-6">
    <div class="flex gap-4 flex-col sm:flex-row">
      <router-link
        to="/task3/new"
        class="bg-primary-100 text-primary-800 font-medium rounded-md border-2 border-primary-200 h-12 shadow-sm px-3 flex gap-1.5 items-center focus:outline-none focus:border-primary-500 focus:ring-3 focus:ring-primary-300"
      >
        <div class="i-heroicons-outline:plus" />New Note
      </router-link>
      <div
        class="flex-grow bg-white rounded-md flex border-2 border-primary-200 h-12 shadow-sm px-3 gap-3 items-center focus-within:outline-none focus-within:border-primary-500 focus-within:ring-3 focus-within:ring-primary-300"
      >
        <div class="h-6 text-primary-700 w-6 i-heroicons-outline:search" />
        <input
          v-model="input"
          class="flex-grow h-full fill-primary-700 focus:outline-none"
          autofocus
        >
      </div>
      <router-link
        to="/task4"
        class="bg-tertiary-100 text-tertiary-800 font-medium rounded-md border-2 border-tertiary-200 h-12 shadow-sm px-3 flex gap-1.5 items-center focus:outline-none focus:border-primary-500 focus:ring-3 focus:ring-primary-300"
      >
        <div class="i-heroicons-outline:chevron-right" />Next task
      </router-link>
    </div>
    <div v-if="isFetching" class="min-h-xs grid place-items-center">
      <div class="i-tabler:loader-quarter animate-spin w-6 h-6 text-primary-800" />
    </div>
    <div v-else-if="results.length > 0" class="grid gap-4 md:min-w-screen-sm lg:grid-cols-2">
      <Note v-for="(result, index) in results" :key="index" :note="result.item" />
    </div>
    <div v-else class="min-h-xs grid place-items-center text-2xl text-primary-700 font-medium tracking-wide">
      No results
    </div>
  </div>
</template>
