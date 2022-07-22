<template>
  <div
    class="w-full max-w-[300px] box-border bg-white flex items-center justify-center gap-2 p-2"
  >
    <p class="text-black">following the value</p>
    <CustomRange
      :max="max"
      mode="live"
      :value="rangeValue1"
      @change-value="updateRangeValue1"
    />

    <p class="text-black">
      {{ rangeValue1 }}
    </p>
  </div>

  <div
    class="w-full max-w-[300px] box-border bg-white flex items-center justify-center gap-2 p-2"
  >
    <p class="text-black">mode='live'</p>
    <CustomRange
      :max="max"
      mode="live"
      :value="rangeValue2"
      @change-value="updateRangeValue2"
    />

    <p class="text-black">
      {{ rangeValue2 }}
    </p>
  </div>

  <div
    class="w-full max-w-[300px] box-border bg-white flex items-center justify-center gap-2 p-2"
  >
    <p class="text-black">default mode</p>
    <CustomRange
      :max="99"
      :value="rangeValue3"
      @change-value="updateRangeValue3"
    />

    <p class="text-black">
      {{ rangeValue3 }}
    </p>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import CustomRange from "./components/CustomRange.vue";
// html elements
const rangeValue1 = ref(0);
const rangeValue2 = ref(0);
const rangeValue3 = ref(0);
// max value
const max = 777;
function updateRangeValue1(newValue: number): void {
  rangeValue1.value = +newValue;
}
function updateRangeValue2(newValue: number): void {
  rangeValue2.value = +newValue;
}
function updateRangeValue3(newValue: number): void {
  rangeValue3.value = +newValue;
}
let int;
int = setInterval(() => {
  rangeValue1.value++;
}, 100);
</script>

<style lang="scss">
@font-face {
  font-family: "Poppins";
  font-style: normal;
  font-weight: 500;
  src: url("@/assets/font/montserrat.woff2");
}

html {
  font-family: Poppins, sans-serif;
}
html,
body,
#app {
  padding: 0;
  margin: 0;
  width: 100%;
  height: 100%;
}
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(54deg, rgb(100, 221, 243), rgb(20, 17, 217));
  flex-direction: column;
}
p {
  margin: 0;
  padding: 0;
  flex: 1;
}
</style>
