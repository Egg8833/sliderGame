<template>
  <div class="containerBox">
    <h2>眼明手快挑戰</h2>
    <p>進行方式，按下"空白鍵"，嘗試組合回原先的圖片</p>
    <div class="slider">
      <sliderList
        :count="count"
        :translateX="translateX"
        :transitionDur="transitionDur"
        :times="times"
        :intervalId="topIntervalId"
        :imgPosition="'top'"
        :changeDirection="'left'"
        :spaceKey="spaceKey"
        @restartBtn="restartBtn"
        @spaceKeyFn="spaceKeyFn"
      ></sliderList>
      <sliderList
        :count="count"
        :translateX="translateX"
        :transitionDur="transitionDur"
        :times="times"
        :intervalId="centerInterval"
        :imgPosition="'center'"
        :changeDirection="'right'"
        :spaceKey="spaceKey"
        @restartBtn="restartBtn"
        @spaceKeyFn="spaceKeyFn"
      ></sliderList>
      <sliderList
        :count="count"
        :translateX="translateX"
        :transitionDur="transitionDur"
        :times="times"
        :intervalId="bottomInterval"
        :imgPosition="'bottom'"
        :changeDirection="'left'"
        :spaceKey="spaceKey"
        @restartBtn="restartBtn"
        @spaceKeyFn="spaceKeyFn"
      ></sliderList>
    </div>

    <div class="level">
      <div>目前難度: {{ level }}</div>
      <div class="time">
        花費時間:
        <span v-show="!disableBtn">
          {{ timer }}
        </span>
        <span v-show="disableBtn" class="show">{{ showTimer }}</span> 秒
      </div>
      <div @keydown.space.prevent="handleSpaceKey">
        難度挑戰:
        <button @click="restartInterval(80)">地獄級</button>
        <button @click="restartInterval(180)">困難</button>
        <button @click="restartInterval(250)">正常</button>
        <button @click="restartInterval(600)">簡單</button>
      </div>
    </div>

    <button
      class="btn"
      @click="restartInterval(times)"
      :disabled="!disableBtn"
      :class="{ active: !disableBtn }"
    >
      重新開始
    </button>
  </div>
</template>

<script setup>
import sliderList from "./sliderList.vue";
import { computed, ref, onMounted } from "vue";

const count = ref(0);
const translateX = ref(33);
const transitionDur = ref(250);
const times = ref(250);
const disableBtn = ref(false);

let spaceKey = ref(false);
let restartCount = 0;
let topIntervalId = "top";
let centerInterval = "center";
let bottomInterval = "bottom";

const levelMap = {
  250: "正常",
  80: "地獄級",
  180: "困難",
  600: "簡單",
};
const level = computed(() => {
  return levelMap[times.value] || "";
});

const restartBtn = (value) => {
  disableBtn.value = value;
  showTimer.value = timer.value;
};
const spaceKeyFn = (value) => {
  spaceKey.value = value;
};

const restartInterval = (time = 600) => {
  restartCount++;
  topIntervalId = restartCount;
  centerInterval = restartCount;
  bottomInterval = restartCount;

  disableBtn.value = false;
  times.value = time;
  resetTimer();
};

const handleSpaceKey = () => {
  alert("目前在「挑戰難度」按鈕上按空白鍵，請移動到按鈕外再嘗試");
  restartInterval(times.value);
  spaceKey.value = true;
};

// 計時器功能
const timer = ref(0);
const showTimer = ref(0);
let intervalIdTime;
const startTimer = () => {
  intervalIdTime = setInterval(() => {
    timer.value++;
  }, 1000);
};
const resetTimer = () => {
  clearInterval(intervalIdTime);
  timer.value = 0;
  startTimer();
};

onMounted(() => {
  startTimer();
});
</script>

<style lang="scss" scoped>
.containerBox {
  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: center;
  height: 80vh;
  position: relative;
  h2 {
    margin-bottom: 10px;
  }
  p {
    margin-top: 10px;
  }
  .slider {
    width: 700px;
    background: lightblue;
    margin: 0 auto;
    overflow: hidden;
    img {
      width: 700px;
      vertical-align: top;
    }
  }
  .row-top {
    width: 100%;
    height: auto;
    display: flex;
  }
  .row-center {
    width: 100%;
    height: auto;
    display: flex;
    transform: translateX(0%);
    transition: transform 0.8s;
    margin-left: -100%;
  }
  .row-bottom {
    width: 100%;
    height: auto;
    display: flex;
    transform: translateX(0%);
    transition: transform 0.8s;
  }
}
.level {
  margin-top: 20px;
  margin-bottom: 20px;
  button {
    margin: 0 10px;
  }
  .time {
    .show {
      font-size: 20px;
      color: red;
    }
  }
}
.btn {
  padding: 10px;
  width: 120px;
  border-radius: 12px;
  background-color: lightskyblue;
  border: 1px solid lightcyan;
  font-size: 18px;
  color: #fff;
  cursor: pointer;
  &.active {
    background-color: gray;
  }
}
</style>