<script setup lang="ts">
import axios from 'axios'
import ThePod from './../components/Pod.vue'
import { computed, reactive } from 'vue'

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
      <v-col cols="auto" class="pa-1" v-for="pod in state.pods" :key="pod?.name">
        <the-pod :pod="pod" />
      </v-col>
    </v-row>
  </v-container>
</template>
