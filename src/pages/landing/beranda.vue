<script setup>
import { Button } from "@/components/ui/button";
</script>
<template>
  <section class="bg-background pt-10">
    <div
      class="grid max-w-screen-xl px-5 py-10 mx-auto lg:gap-8 xl:gap-0 lg:py-16 lg:grid-cols-12"
    >
      <div class="mr-auto place-self-center lg:col-span-7">
        <h1
          class="max-w-2xl mb-4 text-3xl font-extrabold leading-none dark:text-white"
        >
          PENGELOMPOKAN PUPUK BERSUBSIDI MENGGUNAKAN METODE K-MEDOIDS CLUSTERING
        </h1>
        <p
          class="max-w-2xl mb-6 font-light text-gray-500 lg:mb-8 md:text-lg lg:text-xl dark:text-gray-400"
        >
          Untuk Contoh data excel yang digunakan bisa didownload pada Page
          Hitung pada link Dibawah ini. Contoh data diambil dari Pupuk
          Bersubsidi yang berada didaerah Bojonegoro
        </p>
        <RouterLink to="/hitung">
          <Button>
            Get started
            <svg
              class="w-5 h-5 ml-2 -mr-1"
              fill="currentColor"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                d="M10.293 3.293a1 1 0 011.414 0l6 6a1 1 0 010 1.414l-6 6a1 1 0 01-1.414-1.414L14.586 11H3a1 1 0 110-2h11.586l-4.293-4.293a1 1 0 010-1.414z"
                clip-rule="evenodd"
              ></path>
            </svg>
          </Button>
        </RouterLink>
      </div>
      <div class="hidden lg:mt-0 lg:col-span-5 lg:flex">
        <img
          src="/preview.png"
          alt="mockup"
          class="shadow-xl rounded-xl -rotate-[5deg]"
        />
      </div>
    </div>
  </section>
</template>
