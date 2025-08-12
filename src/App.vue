<script setup>
import Header from "./components/header.vue";
import Uses from "./components/uses.vue";
import Projects from "./components/projects.vue";
import Footer from "./components/footer.vue";
import Contact from "./components/contact.vue";
</script>

<template>
  <div class="sm:pt-20 max-w-screen-lg mx-auto p-5 relative">
    <div class="z-0 absolute -mt-10 right-0 text-[10rem] opacity-10 select-none"></div>
    <div class="relative mb-10"><Header /></div>
    <div class="mb-10"><Uses /></div>
    <div class="z-0 absolute mt-10 text-[10rem] opacity-10 select-none"></div>
    <div class="relative mb-10"><Projects /></div>
    <div class="relative mb-10"><Contact /></div>
    <Footer />
  </div>
</template>
