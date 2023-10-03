<template>
  <v-card
    class="pod-card"
    width="250"
    flat
    variant="outlined"
    density="compact"
    prepend-icon="mdi-cube"
  >
    <template v-slot:title>
      <span class="text-subtitle-1">{{ pod?.metadata?.generateName || pod?.metadata?.name }}</span>
      <v-divider />
    </template>
    <v-card-text class="d-flex">
      <div class="mt-0 pa-0">
        <v-icon
          v-for="condition in pod?.status?.conditions"
          :key="`${condition.status}-${condition.type}`"
          :color="condition.status === 'True' ? 'success' : 'error'"
          :icon="condition.status === 'True' ? 'mdi-check-bold' : 'mdi-close'"
          :size="10"
        ></v-icon>
      </div>
      <v-spacer />
      <v-chip
        :color="pod?.status?.phase === 'Running' ? 'success' : 'error'"
        :prepend-icon="pod?.status?.phase === 'Running' ? 'mdi-check-circle' : 'mdi-close-circle'"
        size="x-small"
      >
        {{ pod?.status?.phase }}
      </v-chip>
    </v-card-text>
    <!-- <v-card-actions>
    <v-dialog width="500">
      <template v-slot:activator="{ props }">
        <v-btn size="x-small" variant="plain" v-bind="props" text="View JSON"> </v-btn>
      </template>

      <template v-slot:default="">
        <v-card title="Pod JSON">
            <pre>{{JSON.stringify(pod, null, 2)}}</pre>
        </v-card>
      </template>
    </v-dialog>
    </v-card-actions> -->
  </v-card>
</template>

<script lang="ts">
export default {
  props: {
    pod: {
      type: Object,
      required: false
    }
  }
}
</script>

<style lang="scss">
.pod-card {
  .v-card-item {
    padding: 1px;
    &__prepend {
      padding-inline-end: 0.3rem;
      padding-inline-start: 0.3rem;
      .v-avatar {
        width: 20px !important;
        height: 20px !important;
        .v-icon {
          color: cadetblue;
        }
      }
    }
  }
}
</style>
