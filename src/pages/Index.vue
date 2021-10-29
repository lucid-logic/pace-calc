<template>
  <q-page class="flex">
    <div style="max-width: 300px">
      <div class="row q-pa-xs">
        <div class="col-12 q-pa-xs bg-primary text-white">Distance</div>
      </div>
      <div class="row q-pa-xs">
        <div class="col-8 q-pa-xs">
          <distance-input v-model="distanceVal"></distance-input>
        </div>
        <div class="col-4 q-pa-xs" style="margin: auto">
          <q-btn dense color="primary" @click="doCalc('distance')"
            >Calculate<br />Distance</q-btn
          >
        </div>
      </div>
      <div class="row q-pa-xs">
        <div class="q-pa-xs">
          <q-btn-dropdown color="secondary" label="Distance Select">
            <q-list>
              <q-item
                v-for="distance in presetDistances"
                :key="distance.distance"
                clickable
                v-close-popup
                @click="usePresetDistance(distance.distance)"
              >
                <q-item-section>
                  <q-item-label>{{ distance.title }}</q-item-label>
                </q-item-section>
              </q-item>
            </q-list>
          </q-btn-dropdown>
        </div>
      </div>

      <div class="row q-pa-xs">
        <div class="col-12 q-pa-xs bg-primary text-white">Pace</div>
      </div>
      <div class="row q-pa-xs">
        <div class="col-8 q-pa-xs">
          <time-input v-model="paceVal" isPace="true" />
        </div>
        <div class="col-4 q-pa-xs" style="margin: auto">
          <q-btn dense color="primary" @click="doCalc('pace')"
            >Calculate<br />Pace</q-btn
          >
        </div>
      </div>

      <div class="row q-pa-xs">
        <div class="col-12 q-pa-xs bg-primary text-white">Total Time</div>
      </div>
      <div class="row q-pa-xs">
        <div class="col-8 q-pa-xs">
          <time-input v-model="timeVal" />
        </div>
        <div class="col-4 q-pa-xs" style="margin: auto">
          <q-btn dense color="primary" @click="doCalc('time')"
            >Calculate<br />Total Time</q-btn
          >
        </div>
      </div>
      <div class="row q-pa-xs">
        <div class="col-2 q-pa-xs"></div>
        <div class="col-8 q-pa-xs">
          <q-btn color="secondary" @click="clearCalculator"
            >Clear Calculator</q-btn
          >
        </div>
        <div class="col-2 q-pa-xs"></div>
      </div>
    </div>
  </q-page>
</template>

<script>
import TimeInput from "src/components/TimeInput.vue";
import { defineComponent, ref, reactive } from "vue";
import { useQuasar } from "quasar";
import DistanceInput from "src/components/DistanceInput.vue";

export default defineComponent({
  components: { TimeInput, DistanceInput },
  name: "PageIndex",

  setup() {
    const $q = useQuasar();

    const paceVal = ref(0);
    const timeVal = ref(0);
    const distanceVal = ref(0);
    const distance = ref(0);

    const presetDistances = [
      { title: "1 mile", distance: 1609 },
      { title: "5km", distance: 5000 },
      { title: "10km", distance: 10000 },
      { title: "Half Marathon", distance: 21098 },
      { title: "Marathon", distance: 42195 },
    ];

    function clearCalculator() {
      distanceVal.value = 0;
      paceVal.value = 0;
      timeVal.value = 0;
    }

    function usePresetDistance(meters) {
      distanceVal.value = meters;
    }

    function doCalc(fieldName) {
      var kmPace = paceVal.value;
      var kmDistance = distanceVal.value / 1000;
      var timeInSeconds = timeVal.value;
      switch (fieldName) {
        case "time":
          if (!kmPace || !kmDistance) {
            showError("To calculate time you must specify pace and distance");
            return;
          }
          timeVal.value = kmPace * kmDistance;
          break;
        case "distance":
          if (!kmPace || !timeInSeconds) {
            showError("To calculate distance you must specify pace and time");
            return;
          }
          var distanceInMeters = parseFloat((timeInSeconds / kmPace) * 1000);
          distanceVal.value = distanceInMeters;
          break;
        case "pace":
          if (!kmDistance || !timeInSeconds) {
            showError("To calculate pace you must specify distance and time");
            return;
          }
          paceVal.value = timeInSeconds / kmDistance;
          break;
      }
    }

    function showError(message) {
      $q.notify({
        message: message,
        caption: "Error",
        color: "negative",
      });
    }

    function distanceUnitChanged() {
      var d =
        (distance.value *
          distanceUnits.filter((unit) => {
            return unit.title === previousDistanceUnit;
          })[0].meters) /
        distanceUnits.filter((unit) => {
          return unit.title === distanceUnit.value;
        })[0].meters;
      distance.value = parseFloat(d).toFixed(getDistancePrecision());
      previousDistanceUnit = distanceUnit.value;
    }

    return {
      distance,
      paceVal,
      timeVal,
      distanceVal,
      presetDistances,
      usePresetDistance,
      doCalc,
      distanceUnitChanged,
      clearCalculator,
    };
  },
});
</script>
<style>
.login-input {
  font-size: 1.5rem;
  line-height: 1.5rem;
}
</style>
