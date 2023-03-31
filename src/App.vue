<script setup lang="ts">
import {onMounted, ref} from "vue";
import LogMessage from "@/components/LogMessage.vue";

const logs = ref([])
const nightMode = ref(false)
onMounted(() => {
  const eventSource = new EventSource('http://localhost:8090/api/logs/watch');
  eventSource.onmessage = function (event) {
    const log = JSON.parse(event.data)
    logs.value.push(log)
  }
})

function setNightMode() {
  nightMode.value = !nightMode.value
  document.body.classList.toggle('night')
}
</script>

<template>
  <main class="h-full w-full">
    <nav class="w-full fixed h-20 top-0 flex justify-center items-center p-2 hotlog--nav">
      <div class="flex-0 w-32"></div>
      <div class="flex-1 flex justify-center items-center">
        <img v-if="!nightMode" class="h-16" src="./assets/logo.png" alt="">
        <img v-else class="h-16" src="./assets/logo-inv.png" alt=""></div>
      <div class="flex-0 w-32 flex justify-center">
        <button class="cursor-pointer text-xs" @click="setNightMode">{{ nightMode ? 'Day mode' : 'Night mode' }}
        </button>
      </div>
    </nav>
    <section class="flex justify-center w-full h-full mt-20 p-6">
      <div v-if="logs.length === 0">
        ⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
        <div class="flex flex-col justify-center items-center gap-2 text-gray-400">
          <span class="text-5xl emoji">🌭</span>
          Waiting for logs...
        </div>
        ⠀⠀⠀⠀
      </div>
      <div class="w-3/4 h-full rounded-md hotlog--logs-container" v-if="logs.length > 0">
        <div v-for="log in logs" :key="log.id" class="flex w-full items-center gap-2 hotlog--log-container">
          <div class="flex items-center gap-2 px-2 py-4">
            <div class="p-1 rounded-md text-xs" :style="`background: ${log.client.color}`">{{ log.client.name }}</div>
            <div class="text-xs">{{ new Date(log.date).toLocaleTimeString() }}</div>
          </div>
          <div class="flex-1 flex flex-col">
            <div v-for="message in log.log">
              <LogMessage :message="message"/>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
