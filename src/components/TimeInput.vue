<template>
  <div class="row q-pa-xs">
    <div v-if="!isPace" class="col-3 q-pa-xs">
      <q-input
        class="login-input"
        v-model="hours"
        @change="fieldChanged($event)"
        @focus="$event.target.select()"
        label="hh"
        type="number"
        maxlength="2"
      />
    </div>

    <div class="col-3 q-pa-xs">
      <q-input
        class="login-input"
        v-model="minutes"
        @change="fieldChanged($event)"
        @focus="$event.target.select()"
        label="mm"
        type="number"
      />
    </div>
    <div class="col-3 q-pa-xs">
      <q-input
        class="login-input"
        v-model="seconds"
        label="ss"
        type="number"
        @change="fieldChanged('s')"
        @focus="$event.target.select()"
      />
    </div>
    <div class="col-6 q-pa-xs" v-if="isPace">
      <q-select
        v-model="paceUnit"
        :options="paceOptions"
        @update:model-value="paceUnitChanged"
      />
    </div>
  </div>
</template>

<script>
import { ref, reactive, watchEffect } from "vue";

export default {
  // name: 'ComponentName',
  props: ["modelValue", "isPace"],
  emits: ["update:modelValue"],
  setup(props, context) {
    var secondsPerKm = 0;
    const paceVal = reactive({ minutes: 0, seconds: 0, unit: "mins/km" });
    const paceOptions = ["mins/km", "mins/mile"];
    const paceUnit = ref(paceOptions[0]);
    var previousPaceUnit = paceUnit.value;

    const hours = ref("00");
    const minutes = ref("00");
    const seconds = ref("00");

    watchEffect(() => {
      secondsPerKm = props.modelValue;
      updateFields();
    });

    //Fired when pace/time recalculated
    function updateFields() {
      var timeInSeconds =
        paceUnit.value === "mins/km" ? secondsPerKm : secondsPerKm * 1.609;

      if (!props.isPace) {
        var h = Math.floor(timeInSeconds / 3600);
        hours.value = String(h).padStart(2, "0");
        timeInSeconds -= h * 3600;
      }
      var m = Math.floor(timeInSeconds / 60);
      var s = parseInt(timeInSeconds - m * 60);
      minutes.value = String(m).padStart(2, "0");
      seconds.value = String(s).padStart(2, "0");
    }

    //Fired when the user changes a number is hh:mm:ss
    function fieldChanged() {
      if (isNaN(parseInt(seconds.value))) {
        seconds.value = "00";
      }
      if (isNaN(parseInt(minutes.value))) {
        minutes.value = "00";
      }
      if (isNaN(parseInt(hours.value))) {
        hours.value = "00";
      }
      var timeInSeconds = 0;
      if (!props.isPace) {
        timeInSeconds = parseInt(hours.value) * 3600;
      }
      timeInSeconds += parseInt(minutes.value) * 60 + parseInt(seconds.value);
      secondsPerKm =
        paceUnit.value === "mins/km" ? timeInSeconds : timeInSeconds / 1.609;
      context.emit("update:modelValue", secondsPerKm);
    }

    function paceUnitChanged() {
      updateFields();
    }

    return {
      paceVal,
      paceOptions,
      paceUnit,
      paceUnitChanged,
      minutes,
      seconds,
      hours,
      fieldChanged,
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
