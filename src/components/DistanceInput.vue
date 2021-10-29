<template>
  <div class="row q-pa-xs">
    <div class="col-6 q-pa-xs">
      <q-input
        class="login-input"
        round
        flat
        v-model="distance"
        type="number"
        @change="fieldChanged($event)"
        @focus="$event.target.select()"
      />
    </div>
    <div class="col-6 q-pa-xs">
      <q-select
        v-model="distanceUnit"
        :options="distanceOptions"
        @update:model-value="distanceUnitChanged"
      />
    </div>
  </div>
</template>

<script>
import { ref, reactive, watchEffect } from "vue";

export default {
  // name: 'ComponentName',
  props: ["modelValue"],
  emits: ["update:modelValue"],
  setup(props, context) {
    var distanceInMeters = 0;

    const distance = ref("0");

    const distanceUnits = [
      { title: "kms", meters: 1000, precision: 3 },
      { title: "meters", meters: 1, precision: 0 },
      { title: "miles", meters: 1609, precision: 3 },
      { title: "yards", meters: 0.9144, precision: 0 },
    ];

    const distanceOptions = distanceUnits.map((a) => a.title);

    const distanceUnit = ref(distanceOptions[0]);

    watchEffect(() => {
      distanceInMeters = props.modelValue;
      updateFields();
    });

    function updateFields() {
      distance.value = parseFloat(
        distanceInMeters /
          distanceUnits.filter((unit) => {
            return unit.title === distanceUnit.value;
          })[0].meters
      ).toFixed(getDistancePrecision());
    }

    function fieldChanged() {
      if (isNaN(parseFloat(distance.value))) {
        distance.value = "00";
      }

      var distanceInMeters =
        distance.value *
        distanceUnits.filter((unit) => {
          return unit.title === distanceUnit.value;
        })[0].meters;

      context.emit("update:modelValue", distanceInMeters);
    }

    function distanceUnitChanged() {
      updateFields();
    }

    function getDistancePrecision() {
      return parseInt(
        distanceUnits.filter((unit) => {
          return unit.title === distanceUnit.value;
        })[0].precision
      );
    }

    return {
      distance,
      distanceUnit,
      distanceOptions,
      fieldChanged,
      distanceUnitChanged,
    };
  },
};
</script>

<style>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
}

.login-input {
  font-size: 1.5rem;
  line-height: 1.5rem;
}
</style>
