<script setup>
import { document } from "postcss";
import { ref, onMounted, onUnmounted } from "vue";

const ws = ref(null);
const status = ref("text-nova-gray");

// default: lagu favorit
const spotify = ref({
  track_id: "0hA2fXwzTD0hEqX25rCoGH",
  song: "Kids",
  artist: "Hindia",
});

const vscode = ref(null);

const documents = [
  {
    name: "CV Guntur (EN)",
    path: "/pdf/CV_Guntur Wisnu Saputra_en.pdf",
    icon: ["fas", "file-pdf"],
  },
  {
    name: "CV Guntur (ID)",
    path: "/pdf/CV_Guntur Wisnu Saputra_in.pdf",
    icon: ["fas", "file-pdf"],
  },
];

const connectWebSocket = () => {
  ws.value = new WebSocket("wss://api.lanyard.rest/socket");

  ws.value.onopen = () => {
    console.log("WebSocket connection established.");
    ws.value.send(
      JSON.stringify({
        op: 2,
        d: { subscribe_to_id: "748539900793716887" },
      })
    );
  };

  ws.value.onmessage = (event) => {
    const data = JSON.parse(event.data);
    if (data.t === "INIT_STATE" || data.t === "PRESENCE_UPDATE") {
      const presence = data.d;

      // update spotify kalau ada data dari presence
      if (presence.spotify) {
        spotify.value = presence.spotify;
      }

      vscode.value = presence.activities.find((a) => a.name === "Visual Studio Code");

      switch (presence.discord_status) {
        case "online":
          status.value = "text-nova-green";
          break;
        case "idle":
          status.value = "text-nova-yellow";
          break;
        case "dnd":
          status.value = "text-nova-red";
          break;
        case "offline":
          status.value = "text-nova-gray";
          break;
      }
    }
  };

  ws.value.onerror = (error) => console.error("WebSocket error:", error);
  ws.value.onclose = () => console.log("WebSocket connection closed.");
};

onMounted(() => connectWebSocket());
onUnmounted(() => ws.value?.close());
</script>

<template>
  <div class="font-sans font-black text-5xl">
    GunturWS<span :class="['text-2xl', status]">.github.io</span>
  </div>
  <div>
    Guntur Wisnu Saputra as Frontend developer | UI/UX Designer ,
    <a target="_blank" class="underline">free software</a>
    enthusiast. programming, music, and game.
  </div>
  <div class="flex items-center gap-2 text-sm text-nova-gray">
    <font-awesome-icon :icon="['fab', 'spotify']" class="w-4 h-4 mr-0.5" />
    <div>
      i'm currently obsessed with
      <a
        :href="`https://open.spotify.com/track/${spotify.track_id}`"
        target="_blank"
        class="font-black underline"
      >
        {{ spotify.song }} - {{ spotify.artist }} </a
      >.
    </div>
  </div>
  <div class="flex items-center gap-2 text-sm text-nova-gray">
    <font-awesome-icon :icon="['fas', 'code']" class="w-4 h-4 mr-0.5" />
    <div v-if="vscode">
      i'm currently working on
      <span class="font-black">{{ vscode.details }}</span>
      <span v-if="vscode.state">
        in <span class="font-black">{{ vscode.state }}</span></span
      >
    </div>
    <div v-else>i'm not working on anything right now.</div>
  </div>
  <div class="flex items-center gap-2 text-sm text-nova-gray mt-1">
    <font-awesome-icon :icon="['fab', 'figma']" class="w-4 h-4 mr-0.5" />
    <div>
      currently designing on
      <a href="https://www.figma.com/@username" target="_blank" class="font-black underline">
        Figma
      </a>
    </div>
  </div>
  <div class="flex gap-5 mt-5 text-xl">
    <a href="https://github.com/GunturWS" target="_blank"
      ><font-awesome-icon :icon="['fab', 'github']"
    /></a>
    <a href="https://www.linkedin.com/in/gunturwisnusaputra/" target="_blank"
      ><font-awesome-icon :icon="['fab', 'linkedin']"
    /></a>
    <a href="https://discord.com/users/748539900793716887" target="_blank"
      ><font-awesome-icon :icon="['fab', 'discord']"
    /></a>
    <a href="https://www.instagram.com/gntrwsnu_/" target="_blank"
      ><font-awesome-icon :icon="['fab', 'instagram']"
    /></a>
  </div>
  <!-- Tambahan Download CV -->
  <div class="flex items-center gap-2 text-sm text-nova-gray mt-1">
    <font-awesome-icon :icon="['fas', 'file-pdf']" class="w-4 h-4 mr-0.5" />
    <div class="mt-3 flex gap-1">
      <a
        v-for="doc in documents"
        :key="doc.path"
        :href="doc.path"
        download
        class="inline-flex items-center px-4 py-2 bg-nova-gray text-white text-sm font-semibold rounded-lg shadow hover:bg-gray-700 transition-colors duration-200"
      >
        <font-awesome-icon :icon="['fas', 'file-pdf']" class="w-4 h-4 mr-2" />
        {{ doc.name }}
      </a>
    </div>
  </div>
</template>
