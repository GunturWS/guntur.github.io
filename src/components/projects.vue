<script setup>
import { ref, onMounted, computed } from "vue";

const repos = ref([]);
const showAll = ref(false);

onMounted(async () => {
  try {
    const response = await fetch("/projects.json");
    const data = await response.json();
    repos.value = data.sort((a, b) => b.stargazers_count - a.stargazers_count);
  } catch (error) {
    console.error("Error fetching projects:", error);
  }
});

// tampilkan 4 dulu
const displayedRepos = computed(() => {
  return showAll.value ? repos.value : repos.value.slice(0, 4);
});
</script>

<template>
  <div class="mb-2 font-black text-2xl">projects//</div>
  <div class="grid md:grid-cols-2 gap-2">
    <div v-if="!repos.length">projects could not be retrieved.</div>

    <!-- gunakan displayedRepos -->
    <a
      v-for="repo in displayedRepos"
      :key="repo.name"
      :href="repo.html_url"
      target="_blank"
      class="flex flex-col justify-between px-5 py-3 bg-[#202020]/[.3] border-[#504945] border-[0.5px] rounded-lg text-sm"
    >
      <div>
        <div class="flex items-center gap-1 text-nova-gray">
          <img :src="repo.owner.avatar_Github" class="rounded-full w-4" />
          {{ repo.owner.login }}
        </div>

        <div :class="['font-bold', 'text-lg', repo.archived ? 'line-through' : '']">
          {{ repo.name }}
        </div>

        <div class="line-clamp-2">{{ repo.description }}</div>

        <div v-if="repo.image" class="mt-2">
          <img
            :src="repo.image"
            alt="Project Image"
            class="rounded-lg w-full h-[270px] object-cover mt-2"
          />
        </div>
      </div>

      <div class="flex mt-2 gap-5">
        <div>
          <font-awesome-icon :icon="['fas', 'star']" />
          {{ repo.stargazers_count }}
        </div>
        <div>
          <font-awesome-icon :icon="['fas', 'code-branch']" />
          {{ repo.forks_count }}
        </div>
      </div>
    </a>
  </div>

  <!-- tombol selengkapnya -->
  <div class="mt-4 text-center" v-if="repos.length > 4 && !showAll">
    <button
      @click="showAll = true"
      class="px-4 py-2 bg-[#202020]/40 border border-[#504945] rounded-md hover:bg-[#202020]/60 transition"
    >
      Lihat selengkapnya
    </button>
  </div>
</template>
