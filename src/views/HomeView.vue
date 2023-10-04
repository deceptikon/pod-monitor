<script setup lang="ts">
import axios from 'axios'
import PodCard from '../components/PodCard.vue'
import { reactive } from 'vue'

const state = reactive({
  pods: [],
  // filteredPokemon: computed(() => updatePokemon()),
  urlIdLookup: {}
})
const api = axios.create({
  baseURL: '/api/v1',
  timeout: 1000
  // headers: {'X-Custom-Header': 'foobar'}
})
const fetchPods = () => {
  api.get('/pods').then((response) => {
    state.pods = response.data.items // 👈 get just results
  })
}
fetchPods()
</script>

<template>
  <v-container fluid>
    <v-row no-gutters>
      <v-col cols="auto" class="pa-1" v-for="(pod, i) in state.pods" :key="i">
        <pod-card :pod="pod" />
      </v-col>
    </v-row>
  </v-container>
</template>
