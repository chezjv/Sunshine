<script setup>
import { ref } from 'vue'
import { $tp } from '../../../platform-i18n'
import PlatformLayout from '../../../PlatformLayout.vue'
import Checkbox from "../../../Checkbox.vue";

const props = defineProps([
  'platform',
  'config',
])
const config = ref(props.config)
</script>

<template>
  <!--max_bitrate-->
  <div class="mb-3">
    <label for="max_bitrate" class="form-label">{{ $t("config.max_bitrate") }}</label>
    <input type="number" class="form-control" id="max_bitrate" placeholder="0" v-model="config.max_bitrate" />
    <div class="form-text">{{ $t("config.max_bitrate_desc") }}</div>
  </div>

  <!--minimum_fps_target-->
  <div class="mb-3">
    <label for="minimum_fps_target" class="form-label">{{ $t("config.minimum_fps_target") }}</label>
    <input type="number" min="0" max="1000" class="form-control" id="minimum_fps_target" placeholder="0" v-model="config.minimum_fps_target" />
    <div class="form-text">{{ $t("config.minimum_fps_target_desc") }}</div>
  </div>

  <!--adaptive_bitrate-->
  <div class="mb-3">
    <Checkbox id="adaptive_bitrate"
      locale-prefix="config"
      v-model="config.adaptive_bitrate"
      default="false"
    ></Checkbox>
  </div>

  <!--adaptive_bitrate_min_percent-->
  <div class="mb-3">
    <label for="adaptive_bitrate_min_percent" class="form-label">{{ $t("config.adaptive_bitrate_min_percent") }}</label>
    <input type="number" min="5" max="100" class="form-control" id="adaptive_bitrate_min_percent" placeholder="20" v-model="config.adaptive_bitrate_min_percent" />
    <div class="form-text">{{ $t("config.adaptive_bitrate_min_percent_desc") }}</div>
  </div>

  <!--adaptive_bitrate_decrease_percent-->
  <div class="mb-3">
    <label for="adaptive_bitrate_decrease_percent" class="form-label">{{ $t("config.adaptive_bitrate_decrease_percent") }}</label>
    <input type="number" min="5" max="75" class="form-control" id="adaptive_bitrate_decrease_percent" placeholder="25" v-model="config.adaptive_bitrate_decrease_percent" />
    <div class="form-text">{{ $t("config.adaptive_bitrate_decrease_percent_desc") }}</div>
  </div>

  <!--adaptive_bitrate_increase_delay-->
  <div class="mb-3">
    <label for="adaptive_bitrate_increase_delay" class="form-label">{{ $t("config.adaptive_bitrate_increase_delay") }}</label>
    <input type="number" min="1000" max="60000" step="500" class="form-control" id="adaptive_bitrate_increase_delay" placeholder="8000" v-model="config.adaptive_bitrate_increase_delay" />
    <div class="form-text">{{ $t("config.adaptive_bitrate_increase_delay_desc") }}</div>
  </div>
</template>

<style scoped>
.ms-item {
  background-color: var(--bs-dark-bg-subtle);
  font-size: 12px;
  font-weight: bold;
}
</style>
