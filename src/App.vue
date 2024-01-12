<script setup lang="ts">
import {computed, onMounted, ref} from "vue";
import LogMessage from "@/components/LogMessage.vue";

type Client = {
  id: string,
  color: string,
  name: string
}
type Log = {
  client: Client,
  log: string [],
  date: string,
  id: string
}


const logs = ref<Log[]>([])
const filteredLogs = computed(() => {
  if (!clientFilter.value) {
    return logs.value;
  }
  return logs.value.filter(log => {
    return clientFilter.value && log.client.id === clientFilter.value.id;
  })
})
const clientFilter = ref<Client | null>(null)
const clients = ref<Client[]>([])
const nightMode = ref(false)

function registerClient(client: Client) {
  const index = clients.value.findIndex(c => c.id === client.id);
  if (index === -1) {
    clients.value.push(client);
  }
}


onMounted(() => {
  const eventSource = new EventSource('http://localhost:8090/api/logs/watch');
  eventSource.onmessage = function (event) {
    const log = JSON.parse(event.data)
    logs.value.push(log as Log)
    registerClient(log.client as Client)
  }
})

function setNightMode() {
  nightMode.value = !nightMode.value
  document.body.classList.toggle('night')
}

function setClientFilter(client: Client|null) {
  clientFilter.value = client;

}
</script>

<template>
<!--    <div class="flex w-full h-screen bg-gray-800 text-white min-h-screen">-->
<!--      &lt;!&ndash; Sidebar &ndash;&gt;-->
<!--      <aside class="w-1/5 h-full " aria-label="Sidebar">-->
<!--        <div class="overflow-y-auto h-full py-4 px-3 bg-neutral-900 rounded">-->
<!--            <ul class="space-y-2">-->
<!--              <li v-for="client in clients" :key="client.id" @click="setClientFilter(client)" class="flex flex-col">-->
<!--                <div :class="`flex items-center p-2 text-base font-normal text-gray-300 rounded-lg hover:bg-gray-700 cursor-pointer ${clientFilter && (clientFilter.id === client.id) && 'font-semibold' }`">-->
<!--                  <div class="w-4 h-4 rounded-sm" :style="`background: ${client.color}`"></div>-->
<!--                  <div class="">{{ client.name }}</div>-->
<!--                </div>-->
<!--              </li>-->

<!--            </ul>-->
<!--        </div>-->
<!--      </aside>-->

<!--      &lt;!&ndash; Main content &ndash;&gt;-->
<!--      <div class="flex h-full flex-col flex-grow">-->
<!--        &lt;!&ndash; Top Bar &ndash;&gt;-->
<!--        <header class="flex flex-0 justify-between items-center p-4 bg-neutral-800 border-b border-neutral-600">-->
<!--          &lt;!&ndash; Top bar content &ndash;&gt;-->
<!--          &lt;!&ndash; ... &ndash;&gt;-->
<!--        </header>-->

<!--        &lt;!&ndash; Token List &ndash;&gt;-->
<!--        <main class="flex-grow flex-1 p-4 overflow-auto">-->
<!--          <div class="relative overflow-x-auto shadow-md sm:rounded-lg">-->
<!--            <table class="w-full text-sm text-left text-neutral-400">-->
<!--              <thead class="text-xs uppercase bg-neutral-700">-->
<!--              <tr>-->
<!--                <th scope="col" class="px-6 py-3">Name</th>-->
<!--                <th scope="col" class="px-6 py-3">Preview</th>-->
<!--                <th scope="col" class="px-6 py-3">Value</th>-->
<!--                <th scope="col" class="px-6 py-3">Added</th>-->
<!--                <th scope="col" class="px-6 py-3">Last Updated</th>-->
<!--                <th scope="col" class="px-6 py-3">Source</th>-->
<!--              </tr>-->
<!--              </thead>-->
<!--              <tbody>-->
<!--              &lt;!&ndash; Iterate over your color tokens here &ndash;&gt;-->
<!--              <tr class="bg-neutral-800 border-b border-neutral-700">-->
<!--                <td class="px-6 py-4">background-neutral-default</td>-->
<!--                <td class="px-6 py-4">-->
<!--                  <div class="w-6 h-6 bg-blue-600"></div>-->
<!--                </td>-->
<!--                <td class="px-6 py-4">rgba(28, 25, 23, 1)</td>-->
<!--                <td class="px-6 py-4">Jun 10, 2023</td>-->
<!--                <td class="px-6 py-4">1 min ago</td>-->
<!--                <td class="px-6 py-4">Marketing website</td>-->
<!--              </tr>-->
<!--              &lt;!&ndash; Repeat for other tokens &ndash;&gt;-->
<!--              </tbody>-->
<!--            </table>-->
<!--          </div>-->
<!--        </main>-->
<!--      </div>-->
<!--    </div>-->

  <main class="flex flex-col h-full w-full">
    <nav class="w-full h-20 bg-background flex flex-col justify-center items-center gap-6 px-4 ">
      <div class="flex w-full gap-3">
        <div class="w-1/3"></div>
        <div class="w-1/3 flex items-center  justify-center">
        <img class="h-10 logo" src="./assets/logo.png" alt="">
        </div>
        <div class="w-1/3 flex items-center justify-end">
<!--          <button class="cursor-pointer text-xs" @click="setNightMode">{{ nightMode ? 'Day mode' : 'Night mode' }}-->
<!--          </button>-->
        </div>

      </div>
<!--      <div class=" flex flex-col gap-3">-->
<!--        <h1 class="font-bold text-2xl"># Sessions</h1>-->
<!--        <div :class="`flex items-center gap-2 py-3 cursor-pointer ${!clientFilter && 'font-semibold' }`" @click="setClientFilter(null)">-->
<!--          <div class="w-4 h-4 rounded-sm bg-black"></div>-->
<!--          <div class="">All</div>-->
<!--        </div>-->
<!--        <div v-for="client in clients" :key="client.id" @click="setClientFilter(client)" class="flex flex-col">-->
<!--          <div :class="`flex items-center gap-2 py-3 cursor-pointer ${clientFilter && (clientFilter.id === client.id) && 'font-semibold' }`">-->
<!--            <div class="w-4 h-4 rounded-sm" :style="`background: ${client.color}`"></div>-->
<!--            <div class="">{{ client.name }}</div>-->
<!--          </div>-->
<!--        </div>-->
<!--      </div>-->
    </nav>
    <section class="w-full h-screen overflow-hidden  flex justify-center p-3 items-center bg-log-background ">
      <div v-if="logs.length === 0">
        <div class="flex flex-col justify-center items-center gap-4 text-black opacity-50">
          <span class="text-5xl font-bold">No logs.</span>
          <p>
            Install the hotlog client in your app to display logs here.
          </p>
        </div>
      </div>
      <div class="w-3/4 h-full  overflow-y-auto overflow-x-hidden p-3  flex flex-col gap-4 rounded-md "
           v-if="logs.length > 0">
        <div v-for="log in filteredLogs" :key="log.id" class="flex bg-background shadow-sm border-gray-100 py-2 rounded-md w-full items-start gap-8">
          <div class="w-full flex items-start gap-16 px-4 py-2">
            <div class="flex gap-2">
              <div class="w-4 h-4 rounded-sm" :style="`background: ${log.client.color}`"></div>
              <div class="text-xs">{{ log.client.name }}</div>
            </div>
            <div class="text-xs">{{ new Date(log.date).toLocaleTimeString() }}</div>
            <div class="h-full flex-1 flex flex-col">
              <div class="h-full" v-for="message in log.log">
                <LogMessage :message="message"/>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
