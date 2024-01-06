<template>
  <div :class="`row-${propsData.imgPosition}`" :style="sliderStyleObj">
    <img
      :src="`https://s3-us-west-2.amazonaws.com/s.cdpn.io/233661/mario-${propsData.imgPosition}.svg`"
      alt=""
    />

    <img
      :src="`https://s3-us-west-2.amazonaws.com/s.cdpn.io/233661/mario-${propsData.imgPosition}.svg`"
      alt=""
    />

    <img
      :src="`https://s3-us-west-2.amazonaws.com/s.cdpn.io/233661/mario-${propsData.imgPosition}.svg`"
      alt=""
    />
  </div>
</template>

<script setup>
import { computed, ref, onMounted, watch } from "vue";

const propsData = defineProps({
  times: {
    type: Number,
    default: 500,
  },
  count: {
    type: Number,
    default: 0,
  },
  translateX: {
    type: Number,
    default: 33,
  },
  transitionDur: {
    type: Number,
    default: 500,
  },
  intervalId: {
    type: null,
    default: "top",
  },
  imgPosition: {
    type: String,
    default: "top",
  },
  changeDirection: {
    type: String,
    default: "left",
  },
  spaceKey: {
    type: Boolean,
    default: true,
  },
});
const emit = defineEmits(["restartBtn", "spaceKeyFn"]);

const times = computed(() => propsData.times);
const count = ref(propsData.count);
const translateX = ref(propsData.translateX);
const transitionDur = ref(propsData.transitionDur);

let intervalId = propsData.intervalId;

const translateXMove = computed(() => {
  let total = translateX.value * count.value * -1;
  return total === -99 ? -100 : total;
});

const sliderStyleObj = computed(() => {
  return {
    transform: `translateX(${translateXMove.value}%) `,
    "transition-duration": `${transitionDur.value}ms`,
  };
});

const changeSlideLeft = () => {
  if (count.value < 3) {
    transitionDur.value = times.value;
    count.value = count.value + 1;
  } else {
    count.value = 0;
    transitionDur.value = 0;
  }
};
const changeSlideRight = () => {
  if (count.value > 0) {
    transitionDur.value = times.value;
    count.value = count.value - 1;
  } else {
    count.value = 3;
    transitionDur.value = 0;
  }
};

let SpaceCount = ref(0);
const handleKeyPress = (event) => {
  if (event.code === "Space") {
    SpaceCount.value++;
  }
  if (
    event.code === "Space" &&
    SpaceCount.value == 1 &&
    propsData.imgPosition == "top"
  ) {
    clearInterval(intervalId);
  }
  if (
    event.code === "Space" &&
    SpaceCount.value == 2 &&
    propsData.imgPosition == "center"
  ) {
    clearInterval(intervalId);
  }
  if (
    event.code === "Space" &&
    SpaceCount.value == 3 &&
    propsData.imgPosition == "bottom"
  ) {
    clearInterval(intervalId);
    emit("restartBtn", true);
  }
};

// 重新開始
watch(
  () => propsData.intervalId,
  () => {
    clearInterval(intervalId);
    setTimeout(() => {
      init();
    }, 50);
    SpaceCount.value = 0;
  }
);

// 避免在挑戰按鈕上按的空白鍵多計算一次
watch(
  () => propsData.spaceKey,
  (newD, oldD) => {
    if (newD) {
      emit("spaceKeyFn", false);
      setTimeout(() => {
        SpaceCount.value = 0;
      }, 50);
    }
  }
);

const init = () => {
  const changeDirection = propsData.changeDirection;
  intervalId = setInterval(() => {
    changeDirection === "left" ? changeSlideLeft() : changeSlideRight();
  }, times.value);
};

onMounted(() => {
  window.addEventListener("keydown", handleKeyPress);
  // 在元件掛載後開始監聽鍵盤事件
  init();
});
</script>

<style lang="scss" scoped>
</style>