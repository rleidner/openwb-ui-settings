<template>
  <openwb-base-card
    subtype="warning"
    :collapsible="true"
    :collapsed="true"
  >
    <template #header>
      <font-awesome-icon
        fixed-width
        :icon="['fas', 'car-battery']"
      />
      {{ battery.name }} (ID: {{ battery.id }})
    </template>
    <openwb-base-alert
      :subtype="
        statusLevel[
          $store.state.mqtt[
            'openWB/bat/' + battery.id + '/get/fault_state'
          ]
        ]
      "
    >
      <font-awesome-icon
        v-if="
          $store.state.mqtt[
            'openWB/bat/' + battery.id + '/get/fault_state'
          ] == 1
        "
        fixed-width
        :icon="['fas', 'exclamation-triangle']"
      />
      <font-awesome-icon
        v-else-if="
          $store.state.mqtt[
            'openWB/bat/' + battery.id + '/get/fault_state'
          ] == 2
        "
        fixed-width
        :icon="['fas', 'times-circle']"
      />
      <font-awesome-icon
        v-else
        fixed-width
        :icon="['fas', 'check-circle']"
      />
      Modulmeldung:<br>
      {{
        $store.state.mqtt["openWB/bat/" + battery.id + "/get/fault_str"]
      }}
    </openwb-base-alert>
    <openwb-base-heading>Aktuelle Werte</openwb-base-heading>
    <openwb-base-text-input
      title="Leistung"
      readonly
      class="text-right text-monospace"
      step="0.001"
      unit="kW"
      :model-value="
        formatNumberTopic(
          'openWB/bat/' + battery.id + '/get/power',
          3,
          3,
          0.001,
        )
      "
    />
    <openwb-base-number-input
      title="Ladestand"
      readonly
      class="text-right text-monospace"
      unit="%"
      :model-value="
        $store.state.mqtt['openWB/bat/' + battery.id + '/get/soc']
      "
    />
    <openwb-base-heading>Zählerstände</openwb-base-heading>
    <openwb-base-text-input
      title="Ladung"
      readonly
      class="text-right text-monospace"
      step="0.001"
      unit="kWh"
      :model-value="
        formatNumberTopic(
          'openWB/bat/' + battery.id + '/get/imported',
          3,
          3,
          0.001,
        )
      "
    />
    <openwb-base-text-input
      title="Entladung"
      readonly
      class="text-right text-monospace"
      step="0.001"
      unit="kWh"
      :model-value="
        formatNumberTopic(
          'openWB/bat/' + battery.id + '/get/exported',
          3,
          3,
          0.001,
        )
      "
    />
  </openwb-base-card>
</template>

<script>
import ComponentState from "../mixins/ComponentState.vue";

import { library } from "@fortawesome/fontawesome-svg-core";
import {
  faCheckCircle as fasCheckCircle,
  faExclamationTriangle as fasExclamationTriangle,
  faTimesCircle as fasTimesCircle,
  faCarBattery as fasCarBattery,
} from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

library.add(
  fasCheckCircle,
  fasExclamationTriangle,
  fasTimesCircle,
  fasCarBattery,
);

export default {
  name: "BatteryCard",
  components: {
    FontAwesomeIcon,
  },
  mixins: [ComponentState],
  props: {
    battery: { type: Object, required: true },
  },
  data() {
    return {
      statusLevel: ["success", "warning", "danger"],
    };
  },
};
</script>
