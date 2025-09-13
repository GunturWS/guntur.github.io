<script setup>
import { ref } from "vue";
import emailjs from "@emailjs/browser";

// ambil dari .env
const serviceId = import.meta.env.VITE_EMAILJS_SERVICE_ID;
const templateId = import.meta.env.VITE_EMAILJS_TEMPLATE_ID;
const publicKey = import.meta.env.VITE_EMAILJS_PUBLIC_KEY;

const form = ref({
  name: "",
  email: "",
  message: "",
});

const loading = ref(false);
const success = ref(null);

const sendEmail = async () => {
  loading.value = true;
  success.value = null;

  try {
    const result = await emailjs.send(
      serviceId,
      templateId,
      {
        name: form.value.name,
        email: form.value.email,
        message: form.value.message,
      },
      publicKey
    );

    console.log("SUCCESS!", result.text);
    success.value = true;
    form.value = { name: "", email: "", message: "" }; // reset form
  } catch (error) {
    console.error("FAILED...", error);
    success.value = false;
  } finally {
    loading.value = false;
  }
};

const contacts = ref([
  {
    title: "Email",
    description: "gunturwisnu2003@gmail.com",
    action: () => (window.location.href = "mailto:gunturwisnu2003@gmail.com"),
  },
  {
    title: "Instagram",
    description: "@gntrwsnu",
    action: () => window.open("https://www.instagram.com/gntrwsnu_/", "_blank"),
  },
  {
    title: "LinkedIn",
    description: "Guntur Wisnu Saputra",
    action: () => window.open("https://www.linkedin.com/in/gunturwisnusaputra/", "_blank"),
  },
]);
</script>
<template>
  <div>
    <div class="font-black text-2xl mt-10">Contact/</div>
    <div class="container mx-auto grid grid-cols-1 xl:grid-cols-2 gap-10 mt-10">
      <!-- Kiri -->
      <div class="w-full flex flex-col gap-[30px] h-full">
        <!-- Card 1 -->
        <div
          class="flex flex-col justify-center border-[#504945] border rounded-xl p-[30px] bg-black/10 flex-1"
        >
          <h3 class="text-center w-full">Let's connect!</h3>
          <p class="mx-auto max-w-[336px] text-center">
            Reach out to discuss opportunities, projects, or simply to start a conversation.
          </p>
        </div>

        <!-- Card 2 -->
        <div
          class="flex flex-col border-[#504945] border rounded-xl p-8 bg-black/10 flex-1 justify-center"
        >
          <ul class="flex flex-col gap-4 w-full">
            <li
              v-for="(contact, index) in contacts"
              :key="index"
              class="flex justify-between items-center border-b border-[#504945]/40 pb-2 cursor-pointer hover:text-white transition"
              @click="contact.action"
            >
              <p class="uppercase">{{ contact.title }}</p>
              <h3 class="text-base tracking-tight">
                {{ contact.description }}
              </h3>
            </li>
          </ul>
        </div>
      </div>

      <!-- Kanan (Form) -->
      <div class="w-full flex">
        <form
          @submit.prevent="sendEmail"
          class="flex flex-col gap-4 p-8 bg-black/10 rounded-lg border border-[#504945] flex-1"
        >
          <p class="text-sm mb-4">Fill out the form below to get in touch:</p>

          <input
            v-model="form.name"
            type="text"
            name="name"
            placeholder="Your Name"
            class="bg-black/10 p-3 rounded border-[#504945] border focus:outline-none focus:ring-2 focus:ring-blue-500"
            required
          />

          <input
            v-model="form.email"
            type="email"
            name="email"
            placeholder="Your Email"
            class="bg-black/10 p-3 rounded border-[#504945] border focus:outline-none focus:ring-2 focus:ring-blue-500"
            required
          />

          <textarea
            v-model="form.message"
            name="message"
            placeholder="Your Message"
            class="bg-black/10 p-3 rounded border-[#504945] border focus:outline-none focus:ring-2 focus:ring-blue-500 h-[200px] resize-none"
            required
          ></textarea>

          <button class="max-w-[160px] uppercase mt-2 self-start" type="submit" :disabled="loading">
            {{ loading ? "Sending..." : "Send Email" }}
          </button>

          <p v-if="success === true" class="text-green-500 mt-2">✅ Message sent successfully!</p>
          <p v-else-if="success === false" class="text-red-500 mt-2">
            ❌ Failed to send message. Try again later.
          </p>
        </form>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
