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
      <div class="d-flex">
        <span
          class="text-subtitle-1 pt-1 flex-shrink-1"
          style="max-width: 200px; overflow: hidden; text-overflow: ellipsis"
          >{{ pod?.metadata?.generateName || pod?.metadata?.name }}</span
        >
        <v-spacer />
        <v-menu
          offset="-32"
          location="end center"
          location-strategy="connected"
          transition="slide-y-transition"
        >
          <template v-slot:activator="{ props }">
            <v-btn size="x-small" icon flat variant="flat" v-bind="props">
              <v-icon icon="mdi-dots-vertical" />
            </v-btn>
          </template>

          <v-list
            elevation="0"
            :border="1"
            nav
            density="compact"
            bg-color="#EFEFEF"
            class="dot-menu pa-0"
          >
            <v-list-item
              v-for="(item, index) in menu"
              :key="index"
              :prepend-icon="item.icon"
              density="compact"
              min-height="30"
              link
            >
              <v-list-item-title tag="small">{{ item.title }}</v-list-item-title>
            </v-list-item>
            <v-dialog width="500">
              <template v-slot:activator="{ props }">
                <v-list-item
                  prepend-icon="mdi-code-json"
                  density="compact"
                  min-height="30"
                  link
                  v-bind="props"
                >
                  <v-list-item-title tag="small">JSON</v-list-item-title>
                </v-list-item>
              </template>

              <template v-slot:default="">
                <v-card title="Pod JSON">
                  <pre>{{ JSON.stringify(pod, null, 2) }}</pre>
                </v-card>
              </template>
            </v-dialog>
          </v-list>
        </v-menu>
      </div>
      <v-divider />
    </template>
    <v-card-text>
      <div class="d-flex">
        <v-label
          style="
            padding: 0 55px 0 9px;
            font-size: 8px;
            line-height: 24px;
            text-transform: uppercase;
          "
          >Status:
        </v-label>
        <v-chip
          :color="statusMap(pod?.status?.phase, 'color')"
          size="x-small"
          :text="pod?.status?.phase"
          variant="outlined"
          label
        >
        </v-chip>
      </div>
      <div class="d-flex">
        <v-dialog width="500">
          <template v-slot:activator="{ props }">
            <v-btn
              v-bind="props"
              size="x-small"
              variant="tonal"
              style="
                padding-right: 30px;
                font-size: 8px;
                line-height: 24px;
                text-transform: uppercase;
              "
              >Conditions:
            </v-btn>
          </template>

          <template v-slot:default="">
            <v-card title="Conditions">
              <pre>{{ JSON.stringify(pod?.status?.conditions, null, 2) }}</pre>
            </v-card>
          </template>
        </v-dialog>
        <v-item-group class="mt-0 pa-0" title="Conditions">
          <v-icon
            v-for="condition in pod?.status?.conditions"
            :key="`${condition.status}-${condition.type}`"
            :color="condition.status === 'True' ? 'success' : 'error'"
            :icon="condition.status === 'True' ? 'mdi-check-bold' : 'mdi-close'"
            :size="10"
          ></v-icon>
        </v-item-group>
      </div>
      <div class="d-flex mt-1">
        <v-dialog width="500">
          <template v-slot:activator="{ props }">
            <v-btn
              v-bind="props"
              size="x-small"
              variant="tonal"
              style="
                padding-right: 30px;
                font-size: 8px;
                line-height: 24px;
                text-transform: uppercase;
              "
              >Containers:
            </v-btn>
          </template>

          <template v-slot:default="">
            <v-card title="Containers">
              <pre>{{ JSON.stringify(pod?.status?.containerStatuses, null, 2) }}</pre>
            </v-card>
          </template>
        </v-dialog>
        <v-item-group class="mt-0 pa-0" title="Containers">
          <v-icon
            v-for="cnt in pod?.status?.containerStatuses"
            :key="`${cnt.ready}-${cnt.name}`"
            :color="cnt.ready && cnt.started ? 'success' : cnt.ready ? 'info' : 'error'"
            :icon="
              cnt.ready && cnt.started
                ? 'mdi-check-bold'
                : cnt.ready
                ? 'mdi-timer-outline'
                : 'mdi-close'
            "
            :size="10"
          ></v-icon>
        </v-item-group>
      </div>
      <v-chip
        :prepend-icon="statusMap(pod?.status?.phase, 'icon')"
        :color="statusMap(pod?.status?.phase, 'color')"
        size="x-large"
        style="position: absolute; right: -15px; bottom: 0px; opacity: 0.7"
      >
      </v-chip>
    </v-card-text>
    <!-- <v-card-actions>
      <v-spacer />
      <v-tooltip :text="pod?.status?.phase" location="start">
        <template v-slot:activator="{ props }">
          <v-chip
            v-bind="props"
            :prepend-icon="statusMap(pod?.status?.phase, 'icon')"
            :color="statusMap(pod?.status?.phase, 'color')"
            size="small"
          >
          </v-chip>
        </template>
      </v-tooltip>
    </v-card-actions> -->
  </v-card>
</template>

<script lang="ts">
enum Statuses {
  Default = 'Default',
  Running = 'Running',
  Succeeded = 'Succeeded',
  Warning = 'Warning'
}
interface Keys {
  [name: string]: string
}
interface Map {
  [key: Statuses[any]]: Keys
}
export default {
  data: () => ({
    menu: [{ title: 'Скрыть', icon: 'mdi-eye-off' }]
  }),
  props: {
    pod: {
      type: Object,
      required: false
    }
  },
  computed: {
    conditionsSuccess(): boolean {
      return this.pod?.status?.conditions?.every((c: any) => c.status === 'True')
    }
  },
  methods: {
    statusMap(status: Statuses, key: string) {
      const mapper: Map = {
        [Statuses.Default]: { icon: 'mdi-help-circle', color: 'info' },
        [Statuses.Running]: { icon: 'mdi-check-circle', color: 'success' },
        [Statuses.Warning]: { icon: 'mdi-alert-circle', color: 'warning' },
        [Statuses.Succeeded]: { icon: 'mdi-progress-check', color: 'info' }
      }
      let st: Statuses = status in mapper ? Statuses[status] : Statuses.Default
      if (st === Statuses.Running && !this.conditionsSuccess) st = Statuses.Warning
      return key in mapper[st] ? mapper[st][key] : ''
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
  .v-card-actions {
    min-height: auto;
    padding-top: 0;
  }
}
.dot-menu {
  .v-list-item {
    &-title {
      font-size: 9px !important;
      text-transform: uppercase;
      .v-icon {
        font-size: 13px;
      }
    }
    &__prepend {
      .v-list-item__spacer {
        width: 10px !important;
      }
    }
  }
}
</style>
