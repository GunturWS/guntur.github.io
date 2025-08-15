<script setup>
import { ref } from "vue";

const info = ref({
  title: "Email",
  description: "gunturwisnu2003@gmail.com",
  action: () => {
    window.location.href = "mailto:gunturwisnu2003@gmail.com";
  },
});
</script>

<template>
  <div>
    <div class="font-black text-2xl mt-10">Contact/</div>
    <div class="container mx-auto">
      <div class="w-full flex flex-col xl:flex-row justify-center items-center gap-[30px]">
        <div class="w-full h-[524px] max-w-[500px] flex flex-col justify-between gap-6">
          <div
            class="flex flex-col justify-center border-[#504945] border-[0.5px] items-center gap-2 rounded-xl p-[30px] bg-black/10"
          >
            <h3 class="text-center w-full">Lets Get In Touch</h3>
            <p class="mx-w-[336px] mx-auto text-center">
              Reach out to discuss opportunities, projects, or simply to start a conversation.
            </p>
          </div>

          <div
            class="h-full flex flex-col border-[#504945] border-[0.5px] items-center justify-center rounded-xl p-8 bg-black/10"
          >
            <ul class="flex flex-col gap-[30px]">
              comming soon
              <div class="flex-1 flex grid-cols-2 gap-4">
                <p class="uppercase text-center">
                  {{ info.title }}
                </p>
                <h3 class="text-base tracking-tight">{{ info.description }}</h3>
              </div>
            </ul>
          </div>
        </div>
      </div>
      <div class="w-full max-w-[500px] sm:mt-10 mx-auto mt-5">
        <form class="flex flex-col gap-4 p-8 bg-black/10 rounded-lg border border-[#504945]">
          <p class="text-sm mb-4">Fill out the form below to get in touch:</p>

          <input
            type="text"
            name="name"
            placeholder="Your Name"
            class="bg-black/10 p-3 rounded border-[#504945] border-[0.5px] focus:outline-none focus:ring-2 focus:ring-blue-500"
            required
          />

          <input
            type="email"
            name="email"
            placeholder="Your Email"
            class="bg-black/10 p-3 rounded border-[#504945] border-[0.5px] focus:outline-none focus:ring-2 focus:ring-blue-500"
            required
          />

          <textarea
            name="message"
            placeholder="Your Message"
            class="bg-black/10 p-3 rounded border-[#504945] border-[0.5px] focus:outline-none focus:ring-2 focus:ring-blue-500 h-[200px] resize-none"
            required
          ></textarea>

          <Button class="max-w-[160px] uppercase mt-2 self-start" type="submit">
            Send Email
          </Button>
        </form>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
