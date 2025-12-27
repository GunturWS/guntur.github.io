<script setup>
import { ref, onMounted, computed } from "vue";

const repos = ref([]);
const showAll = ref(false);
const selectedRepo = ref(null);
const showModal = ref(false);
const isLoading = ref(false);

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

// Fungsi untuk membuka modal
const openModal = async (repo) => {
  isLoading.value = true;
  selectedRepo.value = repo;

  // Simulasi loading (bisa dihapus jika tidak perlu)
  await new Promise((resolve) => setTimeout(resolve, 150));

  showModal.value = true;
  isLoading.value = false;
  document.body.style.overflow = "hidden";
};

// Fungsi untuk menutup modal
const closeModal = () => {
  showModal.value = false;
  selectedRepo.value = null;
  // Mengembalikan scroll pada body
  document.body.style.overflow = "auto";
};

// Tutup modal dengan ESC key
const handleKeydown = (event) => {
  if (event.key === "Escape") {
    closeModal();
  }
};

// Tambahkan event listener untuk ESC key
onMounted(() => {
  window.addEventListener("keydown", handleKeydown);
});
</script>

<template>
  <div class="mb-2 font-black text-2xl">projects//</div>
  <div class="grid md:grid-cols-2 gap-2">
    <div v-if="!repos.length">projects could not be retrieved.</div>

    <!-- gunakan displayedRepos -->
    <div
      v-for="repo in displayedRepos"
      :key="repo.name"
      class="flex flex-col justify-between px-5 py-3 bg-[#202020]/[.3] border-[#504945] border-[0.5px] rounded-lg text-sm cursor-pointer hover:bg-[#202020]/[.5] transition-colors duration-200"
      @click="openModal(repo)"
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
    </div>
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

  <!-- Loading overlay -->
  <Transition name="fade">
    <div
      v-if="isLoading"
      class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 backdrop-blur-sm"
    >
      <div class="flex flex-col items-center">
        <!-- Spinner -->
        <div
          class="w-12 h-12 border-4 border-blue-600 border-t-transparent rounded-full animate-spin mb-4"
        ></div>
        <p class="text-gray-300">Loading project details...</p>
      </div>
    </div>
  </Transition>

  <!-- Modal Popup -->
  <div
    v-if="showModal"
    class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/70 backdrop-blur-sm"
    @click.self="closeModal"
  >
    <div
      class="relative w-full max-w-4xl max-h-[90vh] overflow-y-auto bg-[#1a1a1a] border border-[#504945] rounded-lg"
    >
      <!-- Tombol close -->
      <button
        @click="closeModal"
        class="absolute top-4 right-4 z-10 p-2 text-gray-400 hover:text-white transition-colors"
      >
        <font-awesome-icon :icon="['fas', 'times']" class="text-xl" />
      </button>

      <!-- Konten modal -->
      <div v-if="selectedRepo" class="p-6">
        <!-- Header -->
        <div class="flex items-center gap-3 mb-6">
          <img
            :src="selectedRepo.owner.avatar_Github"
            class="rounded-full w-10 h-10"
            alt="Owner Avatar"
          />
          <div>
            <h2 class="text-2xl font-bold">{{ selectedRepo.name }}</h2>
            <p class="text-gray-400">{{ selectedRepo.owner.login }}</p>
          </div>
        </div>

        <!-- Gambar project -->
        <div v-if="selectedRepo.image" class="mb-6">
          <img
            :src="selectedRepo.image"
            alt="Project Image"
            class="rounded-lg w-full h-auto max-h-[400px] object-cover"
          />
        </div>

        <!-- Deskripsi lengkap -->
        <div class="mb-6">
          <h3 class="text-xl font-semibold mb-3">Description</h3>
          <p class="text-gray-300 leading-relaxed">{{ selectedRepo.description }}</p>
        </div>
        <!-- <div class="mb-6">
          <h3 class="text-xl font-semibold mb-3">Full Description</h3>
          <p class="text-gray-300 leading-relaxed">{{ selectedRepo.full_description }}</p>
        </div> -->
        <div class="mb-6">
          <h3 class="text-xl font-semibold mb-3">Tech Stack</h3>
          <p class="text-gray-300 leading-relaxed">{{ selectedRepo.tech_stack }}</p>
        </div>
         <!-- <div class="mb-6">
          <h3 class="text-xl font-semibold mb-3">Tech Stack</h3>
          <p class="text-gray-300 leading-relaxed">{{ selectedRepo.features }}</p>
        </div> -->

        <!-- Stats -->
        <div class="flex gap-6 mb-6">
          <div class="flex items-center gap-2">
            <font-awesome-icon :icon="['fas', 'star']" class="text-yellow-400" />
            <span class="font-semibold">{{ selectedRepo.stargazers_count }} Stars</span>
          </div>
          <div class="flex items-center gap-2">
            <font-awesome-icon :icon="['fas', 'code-branch']" class="text-blue-400" />
            <span class="font-semibold">{{ selectedRepo.forks_count }} Forks</span>
          </div>
          <div v-if="selectedRepo.archived" class="flex items-center gap-2">
            <font-awesome-icon :icon="['fas', 'archive']" class="text-red-400" />
            <span class="font-semibold">Archived</span>
          </div>
        </div>

        <!-- Tombol aksi -->
        <div class="flex gap-4">
          <a
            :href="selectedRepo.html_url"
            target="_blank"
            class="flex-1 py-3 px-4 bg-blue-600 hover:bg-blue-700 text-white text-center rounded-lg transition-colors font-semibold"
          >
            <font-awesome-icon :icon="['fas', 'external-link-alt']" class="mr-2" />
            View Project
          </a>
          <button
            @click="closeModal"
            class="flex-1 py-3 px-4 bg-gray-700 hover:bg-gray-600 text-white rounded-lg transition-colors font-semibold"
          >
            Close
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Animasi untuk modal */
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

/* Scrollbar styling untuk modal */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: #2d2d2d;
}

::-webkit-scrollbar-thumb {
  background: #504945;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #665c54;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.animate-spin {
  animation: spin 1s linear infinite;
}
</style>
