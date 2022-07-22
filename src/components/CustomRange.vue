<template>
  <div
    class="custom-range relative cursor-pointer h-[3px] w-full box-border flex items-center bg-gray-200 focus:outline-none"
    @mousedown.prevent="thumbHandler"
    @focus="showThumb"
    @focusout="hideThumb"
    @mouseenter="showThumb"
    @mouseleave="hideThumb"
    tabindex="0"
    ref="range"
  >
    <div ref="beforethumb" class="beforethumb h-full w-0 bg-[#7945fc]"></div>
    <transition name="thumb">
      <div
        ref="thumb"
        class="thumb opacity-0 absolute w-[12px] h-[12px] rounded-full bg-[#7945fc]"
      ></div>
    </transition>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from "vue";
const emit = defineEmits(["changeValue", "updateValue"]);
const props = defineProps({
  max: Number,
  value: Number,
  mode: String,
});
// html elements
const range = ref<HTMLElement>();
const thumb = ref<HTMLElement>();
const beforethumb = ref<HTMLElement>();
// range value
const value = ref(props.value);
// errored value
const errored = ref(true);
let movePrice: number;
onMounted(() => {
  // values check
  if (range.value == undefined) {
    console.log("range elements wasn't provided");
    return;
  }
  if (thumb.value == undefined) {
    console.log("thumb elements wasn't provided");
    return;
  }
  if (beforethumb.value == undefined) {
    console.log("beforethumb elements wasn't provided");
    return;
  }

  if (props.max == undefined) {
    console.log("max value wasn't provided");
    return;
  }
  if (props.value == undefined) {
    console.log("value wasn't provided");
    return;
  }
  // set if all values exist
  errored.value = false;
  // range margins
  range.value.style.marginInline = thumb.value.offsetWidth / 2 + "px";
  // default thumb position
  thumb.value.style.left = -(thumb.value.offsetWidth / 2) + "px";
  // movePrice initialization
  movePrice = +(props.max / range.value.clientWidth).toFixed(2);
});
watch(
  () => props.value,
  (newValue, oldValue): void => {
    if (!errored.value && newValue != undefined) {
      // react to props.value change
      if (newValue === oldValue) return;
      if (newValue > props.max) {
        emit("changeValue", (value.value = props.max));
        moveThumb(undefined, value.value);
        return;
      }
      moveThumb(undefined, newValue);
    }
  }
);
const changeValue = (thumbPos: number): void => {
  const newValue = +(thumbPos / movePrice).toFixed(0);
  value.value = newValue;
  // live mode = submit value on every move/change
  if (props.mode === "live") return emit("changeValue", value.value);
  emit("updateValue", value.value);
};
const moveThumb = (event?: MouseEvent, value?: number): void => {
  if (!errored.value) {
    // updating movePrice to handle resize and zoom on the page
    movePrice = +(range.value.clientWidth / props.max).toFixed(2);
    if (value !== undefined) {
      // props value change
      beforethumb.value.style.width = Math.ceil(value * movePrice) + "px";
      thumb.value.style.left =
        Math.ceil(value * movePrice) - thumb.value.offsetWidth / 2 + "px";
      return;
    }
    let newLeft: number =
      event.clientX -
      range.value.getBoundingClientRect().left -
      thumb.value.offsetWidth / 2;
    const rightEdge = range.value.clientWidth - thumb.value.offsetWidth / 2;
    //left side limitation
    if (newLeft < -(thumb.value.offsetWidth / 2)) {
      newLeft = -(thumb.value.offsetWidth / 2);
    }
    // right side limitation
    if (newLeft > rightEdge) {
      newLeft = rightEdge;
    }
    beforethumb.value.style.width =
      newLeft + thumb.value.offsetWidth / 2 + "px";
    thumb.value.style.left = newLeft + "px";
    changeValue(newLeft + thumb.value.offsetWidth / 2);
  }
};
const thumbHandler = (event: MouseEvent): void => {
  if (!errored.value) {
    range.value.focus();
    // click
    moveThumb(event);
    document.addEventListener("mousemove", moveThumb);
    document.addEventListener("mouseup", onMouseUp);
    // remove event listeners
    function onMouseUp() {
      // confirm range value
      emit("changeValue", value.value);
      document.removeEventListener("mouseup", onMouseUp);
      document.removeEventListener("mousemove", moveThumb);
    }
  }
};
// show thumb
const showThumb = (e: Event): void => {
  const target = e.target as HTMLElement;
  const thumb = target.querySelector(".thumb");
  if (thumb) thumb.classList.add("active");
};
// hide thumb
const hideThumb = (e: Event): void => {
  const target = e.target as HTMLElement;
  // keep thumb visible if range is focused
  if (target === document.activeElement) return;
  const thumb = target.querySelector(".thumb");
  if (thumb) thumb.classList.remove("active");
};
</script>

<style lang="scss" scoped>
.thumb {
  transition: opacity 0.2s;
  &.active {
    opacity: 100;
    transition: opacity 0.2s;
  }
}
</style>
