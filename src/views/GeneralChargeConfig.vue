<template>
  <div class="generalChargeConfig">
    <form name="generalChargeConfigForm">
      <openwb-base-card title="Allgemein">
        <div v-if="$store.state.mqtt['openWB/general/extern'] === true">
          <openwb-base-alert subtype="info">
            Diese Einstellungen sind nicht verfügbar, solange sich
            diese openWB im Steuerungsmodus "secondary" befindet.
          </openwb-base-alert>
        </div>
        <div v-else>
          <openwb-base-button-group-input
            title="Begrenzung der Schieflast"
            :buttons="[
              {
                buttonValue: false,
                text: 'Nein',
                class: 'btn-outline-danger',
              },
              {
                buttonValue: true,
                text: 'Ja',
                class: 'btn-outline-success',
              },
            ]"
            :model-value="
              $store.state.mqtt[
                'openWB/general/chargemode_config/unbalanced_load'
              ]
            "
            @update:model-value="
              updateState(
                'openWB/general/chargemode_config/unbalanced_load',
                $event,
              )
            "
          >
            <template #help>
              Wenn diese Option aktiviert ist, werden
              nicht-dreiphasige Ladevorgänge so geregelt, dass am
              Netzanschlusspunkt (EVU-Zähler) die eingestellte
              Grenze für die Schieflast nicht überschritten wird.
              Hierfür muss der EVU-Zähler einzelne Phasenströme
              bereitstellen!<br>
              Weiterhin müssen bei den Ladepunkten sowie
              Fahrzeugen sämtliche Angaben zur Anzahl
              angeschlossener/unterstützter Phasen und der
              Phasenrotation hinterlegt werden, damit der Regelung
              bekannt ist, welcher Ladevorgang welche Phase am
              Netzanschluss beeinflusst. siehe "Konfiguration -
              Ladepunkte -> elektrischer Anschluss - Phase 1" und
              "Konfiguration - Fahrzeuge -> Fahrzeug-Profile"
            </template>
          </openwb-base-button-group-input>
          <openwb-base-range-input
            v-if="
              $store.state.mqtt[
                'openWB/general/chargemode_config/unbalanced_load'
              ]
            "
            title="Erlaubte Schieflast"
            :min="10"
            :max="32"
            :step="1"
            unit="A"
            :model-value="
              $store.state.mqtt[
                'openWB/general/chargemode_config/unbalanced_load_limit'
              ]
            "
            @update:model-value="
              updateState(
                'openWB/general/chargemode_config/unbalanced_load_limit',
                $event,
              )
            "
          >
            <template #help>
              Hiermit wird festgelegt, welche Schieflast am
              Netzanschlusspunkt erlaubt ist. Bei Überschreitung
              werden gezielt einzelne Ladevorgänge in der Leistung
              begrenzt.
            </template>
          </openwb-base-range-input>
          <openwb-base-button-group-input
            title="Phasenumschaltung wiederholt anstoßen"
            :buttons="[
              {
                buttonValue: false,
                text: 'Nein',
                class: 'btn-outline-danger',
              },
              {
                buttonValue: true,
                text: 'Ja',
                class: 'btn-outline-success',
              },
            ]"
            :model-value="
              $store.state.mqtt[
                'openWB/general/chargemode_config/retry_failed_phase_switches'
              ]
            "
            @update:model-value="
              updateState(
                'openWB/general/chargemode_config/retry_failed_phase_switches',
                $event,
              )
            "
          >
            <template #help>
              Wenn diese Option aktiviert ist, werden bis zu drei
              Umschaltversuche vorgenommen, wenn die vorgegebene
              und genutzte Phasenzahl nicht übereinstimmen. Wird
              die Option deaktiviert, wird nur eine Umschaltung
              durchgeführt.<br>
              Die gezählten Fehlversuche werden mit dem Abstecken
              zurückgesetzt.
            </template>
          </openwb-base-button-group-input>
          <openwb-base-range-input
            title="Verzögerung automat. Phasenumschaltung"
            :min="0"
            :max="14"
            :step="1"
            :labels="[
              { label: '1&uArr; 15&dArr; Min.', value: 1 },
              { label: '2&uArr; 14&dArr; Min.', value: 2 },
              { label: '3&uArr; 13&dArr; Min.', value: 3 },
              { label: '4&uArr; 12&dArr; Min.', value: 4 },
              { label: '5&uArr; 11&dArr; Min.', value: 5 },
              { label: '6&uArr; 10&dArr; Min.', value: 6 },
              { label: '7&uArr; 9&dArr; Min.', value: 7 },
              { label: '8&uArr; 8&dArr; Min.', value: 8 },
              { label: '9&uArr; 7&dArr; Min.', value: 9 },
              { label: '10&uArr; 6&dArr; Min.', value: 10 },
              { label: '11&uArr; 5&dArr; Min.', value: 11 },
              { label: '12&uArr; 4&dArr; Min.', value: 12 },
              { label: '13&uArr; 3&dArr; Min.', value: 13 },
              { label: '14&uArr; 2&dArr; Min.', value: 14 },
              { label: '15&uArr; 1&dArr; Min.', value: 15 },
            ]"
            :model-value="
              $store.state.mqtt[
                'openWB/general/chargemode_config/phase_switch_delay'
              ]
            "
            @update:model-value="
              updateState(
                'openWB/general/chargemode_config/phase_switch_delay',
                $event,
              )
            "
          >
            <template #help>
              Um zu viele Umschaltungen zu vermeiden, wird Anhand
              dieses Wertes definiert, wann die Umschaltung
              erfolgen soll. Ist für durchgehend x Minuten die
              Maximalstromstärke erreicht, wird auf mehrphasige
              Ladung &uArr; umgestellt. Ist die Ladung nur für ein
              Intervall unterhalb der Maximalstromstärke, beginnt
              das Intervall für die Umschaltung erneut. Ist die
              Ladung im mehrphasigen Modus für 16 - x Minuten auf
              der Minimalstromstärke, wird wieder auf einphasige
              Ladung &dArr; gewechselt.<br>
              Ist ausreichend Überschuss vorhanden, wird beim
              Ladestart die Umschaltverzögerung nicht abgewartet,
              sondern direkt mit mehrphasiger Ladung begonnen.
            </template>
          </openwb-base-range-input>
          <hr>
          <openwb-base-heading>
            Berechnung der Ladekosten
            <template #help>
              Zur Berechnung der Ladekosten im Lade-Log werden
              stundenweise die Anteile der Stromquellen (Speicher,
              Netz, PV) berechnet und mit den hier angegebenen
              Preisen multipliziert.<br>
              Ist die Abrechnung über das Ladeprotokoll, z.B. mit
              dem Arbeitgeber, vereinbart, ist bei allen drei
              Feldern der vereinbarte Preis einzutragen.
            </template>
          </openwb-base-heading>
          <div
            v-if="
              $store.state.mqtt['openWB/optional/et/provider'] &&
                $store.state.mqtt['openWB/optional/et/provider']
                  .type
            "
          >
            <openwb-base-alert subtype="info">
              Für den Netzbezug wird der dynamische Strompreis des
              Anbieters für variable Stromtarife verwendet
              (stündliche Aktualisierung durch den Anbieter).
            </openwb-base-alert>
          </div>
          <div v-else>
            <openwb-base-number-input
              title="Preis für Netzbezug"
              :step="0.001"
              :precision="3"
              unit="ct/kWh"
              required
              :model-value="
                $store.state.mqtt[
                  'openWB/general/prices/grid'
                ] * 100000
              "
              @update:model-value="
                updateState(
                  'openWB/general/prices/grid',
                  parseFloat(($event / 100000).toFixed(7)),
                )
              "
            />
          </div>
          <openwb-base-number-input
            title="Preis für Speicherentladung"
            :step="0.001"
            :precision="3"
            unit="ct/kWh"
            required
            :model-value="
              $store.state.mqtt['openWB/general/prices/bat'] *
                100000
            "
            @update:model-value="
              updateState(
                'openWB/general/prices/bat',
                parseFloat(($event / 100000).toFixed(7)),
              )
            "
          />
          <openwb-base-number-input
            title="Preis für PV-Strom"
            :step="0.001"
            :precision="3"
            unit="ct/kWh"
            required
            :model-value="
              $store.state.mqtt['openWB/general/prices/pv'] *
                100000
            "
            @update:model-value="
              updateState(
                'openWB/general/prices/pv',
                parseFloat(($event / 100000).toFixed(7)),
              )
            "
          />
        </div>
      </openwb-base-card>
      <openwb-base-card title="Optional">
        <div v-if="$store.state.mqtt['openWB/general/extern'] === true">
          <openwb-base-alert subtype="info">
            Diese Einstellungen sind nicht verfügbar, solange sich
            diese openWB im Steuerungsmodus "secondary" befindet.
          </openwb-base-alert>
        </div>
        <div v-else>
          <openwb-base-heading>
            Variable Stromtarife
          </openwb-base-heading>
          <openwb-base-alert subtype="info">
            Bei Sofort- und Zeitladen wird nur geladen, wenn der
            Strompreis unter dem maximalen angegeben Strompreis
            liegt. Für Zielladen wird die Ladedauer ermittelt und
            dann zu den günstigsten Stunden geladen.<br>
            Wenn keine Preise abgefragt werden können, wird bei
            Sofort- und Zeitladen immer geladen und bei Zielladen
            zunächst mit PV-Überschuss und zum Erreichen des
            Zieltermins mit Netzstrom.
          </openwb-base-alert>
          <openwb-base-select-input
            class="mb-2"
            title="Anbieter"
            :options="electricityTariffList"
            :model-value="
              $store.state.mqtt['openWB/optional/et/provider']
                ? $store.state.mqtt[
                  'openWB/optional/et/provider'
                ].type
                : ''
            "
            @update:model-value="
              updateSelectedElectricityTariff($event)
            "
          />
          <div
            v-if="
              $store.state.mqtt['openWB/optional/et/provider'] &&
                $store.state.mqtt['openWB/optional/et/provider']
                  .type
            "
          >
            <openwb-electricity-tariff-proxy
              :electricity-tariff="
                $store.state.mqtt['openWB/optional/et/provider']
              "
              @update:configuration="
                updateConfiguration(
                  'openWB/optional/et/provider',
                  $event,
                )
              "
            />
          </div>
        </div>
      </openwb-base-card>
      <openwb-base-submit-buttons
        form-name="generalChargeConfigForm"
        @save="$emit('save')"
        @reset="$emit('reset')"
        @defaults="$emit('defaults')"
      />
    </form>
  </div>
</template>

<script>
import ComponentState from "../components/mixins/ComponentState.vue";
import OpenwbElectricityTariffProxy from "../components/electricity_tariffs/OpenwbElectricityTariffProxy.vue";

export default {
  name: "OpenwbGeneralChargeConfigView",
  components: {
    OpenwbElectricityTariffProxy,
  },
  mixins: [ComponentState],
  emits: ['save', 'reset', 'defaults'],
  data() {
    return {
      mqttTopicsToSubscribe: [
        "openWB/general/extern",
        "openWB/general/chargemode_config/phase_switch_delay",
        "openWB/general/chargemode_config/retry_failed_phase_switches",
        "openWB/general/chargemode_config/unbalanced_load",
        "openWB/general/chargemode_config/unbalanced_load_limit",
        "openWB/general/prices/bat",
        "openWB/general/prices/grid",
        "openWB/general/prices/pv",
        "openWB/optional/et/provider",
        "openWB/system/configurable/electricity_tariffs",
      ],
    };
  },
  computed: {
    electricityTariffList() {
      return this.$store.state.mqtt[
        "openWB/system/configurable/electricity_tariffs"
      ];
    },
  },
  methods: {
    getElectricityTariffDefaultConfiguration(electricityTariffType) {
      const electricityTariffDefaults = this.electricityTariffList.find(
        (element) => element.value == electricityTariffType,
      );
      if (
        Object.prototype.hasOwnProperty.call(
          electricityTariffDefaults,
          "defaults",
        )
      ) {
        return { ...electricityTariffDefaults.defaults };
      }
      console.warn(
        "no default configuration found for electricity tariff type!",
        electricityTariffType,
      );
      return {};
    },
    updateSelectedElectricityTariff($event) {
      this.updateState("openWB/optional/et/provider", $event, "type");
      this.updateState(
        "openWB/optional/et/provider",
        this.getElectricityTariffDefaultConfiguration($event),
      );
    },
    updateConfiguration(key, event) {
      console.debug("updateConfiguration", key, event);
      this.updateState(key, event.value, event.object);
    },
  },
};
</script>
