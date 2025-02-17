<template>
  <v-container fluid class="constrained-width px-2 px-sm-4">
    <v-row class="mt-0 mt-sm-2">
      <v-col cols="12" md="6" class="pt-0">

        <collapsable-card
          :title="$t('app.general.title.config_files')"
          icon="$files"
          :draggable="false"
        >
          <file-system
            :roots="['config', 'config_examples']"
            :max-height="620">
          </file-system>
        </collapsable-card>

      </v-col>
      <v-col cols="12" md="6" class="pt-0">

        <klippy-card v-if="!klippyReady || hasWarnings"></klippy-card>
        <printer-stats-card></printer-stats-card>

        <v-row>
          <v-col cols="12" sm="5" v-if="klippyReady">
            <collapsable-card
              :title="$t('app.general.title.system_control')"
              :collapsable="false"
              icon="$cogs">
              <v-card-text>
                <system-control></system-control>
              </v-card-text>
            </collapsable-card>
          </v-col>
          <v-col cols="12" sm="7" v-if="supportsVersions && klippyReady">
            <system-versions
            ></system-versions>
          </v-col>
        </v-row>

      </v-col>
    </v-row>

    <v-row class="mt-0 mt-sm-2">
      <!-- <v-col cols="12" lg="4" v-if="klippyReady">
        <printer-stats-card></printer-stats-card>
      </v-col> -->
      <v-col cols="12" v-if="supportsHistory && klippyReady" >
        <printer-history-card></printer-history-card>
      </v-col>
    </v-row>

  </v-container>
</template>

<script lang="ts">
import { Component, Mixins } from 'vue-property-decorator'
import StateMixin from '@/mixins/state'
import EndStopsCard from '@/components/cards/configuration/EndStopsCard.vue'
import RunoutSensorsCard from '@/components/cards/configuration/RunoutSensorsCard.vue'
import FileSystem from '@/components/widgets/filesystem/FileSystem.vue'
import KlippyCard from '@/components/cards/KlippyCard.vue'
import BedMeshCard from '@/components/cards/configuration/BedMeshCard.vue'
import SystemControl from '@/components/widgets/configuration/SystemControl.vue'
import SystemVersions from '@/components/widgets/versions/SystemVersions.vue'
import PrinterStatsCard from '@/components/cards/configuration/PrinterStatsCard.vue'
import PrinterHistoryCard from '@/components/cards/configuration/PrintHistoryCard.vue'

const BedMeshWidget = () => import(/* webpackChunkName: "bedmesh", webpackPrefetch: true */ '@/components/widgets/configuration/BedMeshWidget.vue')

@Component({
  components: {
    BedMeshCard,
    BedMeshWidget,
    EndStopsCard,
    RunoutSensorsCard,
    FileSystem,
    KlippyCard,
    SystemControl,
    PrinterStatsCard,
    PrinterHistoryCard,
    SystemVersions
  }
})
export default class Configure extends Mixins(StateMixin) {
  get supportsBedMesh () {
    return this.$store.getters['printer/getSupportsBedMesh']
  }

  get supportsRunoutSensors () {
    return this.$store.getters['printer/getRunoutSensors'].length
  }

  get supportsEndstops () {
    const endStops = this.$store.getters['printer/getEndstops']
    return (Object.keys(endStops).length > 0)
  }

  get supportsHistory () {
    return this.$store.getters['server/pluginSupport']('history')
  }

  get supportsVersions () {
    return this.$store.getters['server/pluginSupport']('update_manager')
  }
}
</script>
