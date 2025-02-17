<template>
  <v-dialog
    :value="value"
    :max-width="500"
    persistent
  >
    <v-form
      ref="form"
      @submit.prevent="handleSave(camera)"
      v-model="valid"
    >
      <v-card>
        <v-card-title class="secondary py-2">
          <span class="focus--text">{{ (camera.id != -1) ? $t('app.general.label.edit_camera') : $t('app.general.label.add_camera') }}</span>
        </v-card-title>

        <v-divider />

        <fluidd-setting :title="$t('app.setting.label.enable')">
          <v-switch
            class="mt-0"
            hide-details="auto"
            v-model="camera.enabled">
          </v-switch>
        </fluidd-setting>

        <v-divider />

        <fluidd-setting :title="$t('app.general.label.name')">
          <v-text-field
            filled
            dense
            class="mt-0"
            hide-details="auto"
            :rules="[rules.required]"
            v-model="camera.name">
          </v-text-field>
        </fluidd-setting>

        <v-divider />

        <fluidd-setting :title="$t('app.setting.label.camera_flip_x')">
          <v-switch
            hide-details
            v-model="camera.flipX">
          </v-switch>
        </fluidd-setting>

        <v-divider />

        <fluidd-setting :title="$t('app.setting.label.camera_flip_y')">
          <v-switch
            class="mb-4"
            hide-details
            v-model="camera.flipY">
          </v-switch>
        </fluidd-setting>

        <v-divider />

        <fluidd-setting :title="$t('app.setting.label.camera_stream_type')">
          <v-select
            filled
            dense
            hide-details="auto"
            :items="[
              { text: $t('app.setting.camera_type_options.mjpegadaptive'), value: 'mjpgadaptive' },
              { text: $t('app.setting.camera_type_options.mjpegstream'), value: 'mjpgstream' },
              { text: $t('app.setting.camera_type_options.video'), value: 'ipstream' }
            ]"
            item-value="value"
            item-text="text"
            v-model="camera.type">
          </v-select>
        </fluidd-setting>

        <v-divider />

        <fluidd-setting v-if="camera.type === 'mjpgadaptive'" :title="$t('app.setting.label.fps_target')">
          <v-text-field
            class="mt-5"
            filled
            dense
            single-line
            hide-details="auto"
            v-model.number="camera.fpstarget"
            :rules="[rules.required]"
          ></v-text-field>
        </fluidd-setting>

        <v-divider v-if="camera.type === 'mjpgadaptive'" />

        <fluidd-setting :title="$t('app.setting.label.camera_url')">
          <v-text-field
            class="mt-5"
            filled
            dense
            single-line
            hide-details="auto"
            v-model="camera.url"
            :rules="[rules.required]"
          ></v-text-field>
        </fluidd-setting>

        <v-divider />

        <v-card-actions>
          <v-spacer></v-spacer>
          <btn color="warning" text @click="$emit('input', false)" type="button">{{ $t('app.general.btn.cancel') }}</btn>
          <btn color="primary" type="submit">{{ (camera.id !== -1) ? $t('app.general.btn.save') : $t('app.general.btn.add') }}</btn>
        </v-card-actions>
      </v-card>
    </v-form>
  </v-dialog>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator'
import { CameraConfig } from '@/store/cameras/types'

@Component({})
export default class CameraDialog extends Vue {
  @Prop({ type: Boolean, required: true })
  value!: boolean

  @Prop({ type: Object, required: true })
  camera!: CameraConfig

  cameraUrlRules = [
    (v: string) => !!v || 'Required'
  ]

  valid = false

  rules = {
    required: (v: string) => (v !== undefined && v !== '') || this.$t('app.general.simple_form.error.required'),
    minFps: (v: number) => (v >= 1) || this.$t('app.general.simple_form.error.min', { min: 1 }),
    maxFps: (v: number) => (v <= 60) || this.$t('app.general.simple_form.error.max', { max: 60 })
  }

  handleSave () {
    if (this.valid) {
      this.$emit('save', this.camera)
      this.$emit('input', false)
    }
  }
}
</script>
