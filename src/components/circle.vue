<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  prizeData: {
    type: Array,
    default: () => []
  }
})

const prizeNum = ref(0)
prizeNum.value = props.prizeData.length;
const prizeRotate = ref(0)
const skewY = ref(0)
prizeRotate.value = 360 / prizeNum.value;

if(prizeRotate.value > 90){
  if(prizeNum.value == 3){
    skewY.value = 30;
  }else{
    skewY.value = 0;
  }
}else{
  skewY.value =  -(90 - prizeRotate.value);
}

const rotateNum = ref(0);
const isRunning = ref(false);
const runningClass = ref('')
const rotate360Times = 8;
const waitTime = 5;
const isPlay = ref(false);
const prizeIndex = ref(0);

const runHandler = () => {
  if(isRunning.value){
    return;
  }
  isRunning.value = true;
  const randomNum = Math.floor(Math.random() * 720) + 1;
  rotateNum.value = randomNum + 360 * rotate360Times;

  runningClass.value = `transform: rotate(-${rotateNum.value}deg); transition: ${waitTime}s ease-in-out;`;
  let timer = setTimeout(() => {
    isPlay.value = true;
    isRunning.value = false;
    rotateNum.value = rotateNum.value % 360;
    console.log(rotateNum.value, prizeRotate.value);
    // 取得抽到的獎品是哪個
    prizeIndex.value = Math.floor((rotateNum.value + prizeRotate.value / 2) % 360 / prizeRotate.value);

    clearTimeout(timer);
  }, waitTime * 1000);
}

// watch props
watch(props.prizeData, (newVal) => {
  console.log(newVal);
  prizeNum.value = newVal.length;
  prizeRotate.value = 360 / prizeNum.value;
  if(prizeRotate.value > 90){
    if(prizeNum.value == 3){
      skewY.value = 30;
    }else{
      skewY.value = 0;
    }
  }else{
    skewY.value =  -(90 - prizeRotate.value);
  }

  prizeIndex.value = 0;
  isPlay.value = false;
  isRunning.value = false;
  runningClass.value = '';
  rotateNum.value = 0;
})

</script>

<template>
  <div class="game-box">
    <div class="prize-info">
      <p v-if="!isPlay && !isRunning">
        請點擊抽獎
      </p>
      <p v-else-if="isRunning">
        抽獎中...
      </p>
      <p v-else>
        恭喜抽到 {{ prizeIndex+1 }} "{{ props.prizeData[prizeIndex].name }}" <br>點擊再次抽獎
      </p>
    </div>
    <div class="circle-container" :style="isRunning?runningClass:`transform: rotate(-${rotateNum}deg);`">
      <div 
        class="prize-container"
        :class="{
          'one-prize': prizeNum == 1,
          'two-prize': prizeNum == 2,
          'odd-color': (prizeNum % 2 !== 0 && key==1)
        }"
        v-for="key in prizeNum"
        :style="`transform: rotate(${(key-1)*prizeRotate - prizeRotate/2}deg) skewY(${skewY}deg);`"
      ></div>
      <div class="prize" v-for="(key,index) in prizeNum"
        :style="`transform: rotate(${(index)*prizeRotate}deg)`">
        <div class="prize-child">
          <div>{{ key }}</div>
          <p> {{props.prizeData[index].name}} </p>
        </div>
      </div>
    </div>
    <div class="light-div" v-for="key in 16" :style="`transform: rotate(${key * 22.5}deg)`">
      <div></div>
    </div>
    <div class="run-btn" @click="runHandler()">
      抽
      <span></span>
    </div>
  </div>
</template>

<style lang="scss" scoped>

@keyframes lightShining {
  0% {
    background: #fff;
    box-shadow: 0 0 10px 5px rgba(255, 255, 255, 0.5);
  }
  50% {
    background: red;
    box-shadow: 0 0 20px 10px rgba(255, 255, 255, 0.5);
  }
  100% {
    background: #fff;
    box-shadow: 0 0 10px 5px rgba(255, 255, 255, 0.5);
  }
  
}

.game-box {
  position: relative;
  margin: 50px auto 0 auto;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 300px;
  height: auto;
  min-height: 450px;
  border-radius: 50%;
  .prize-info{
    position: absolute;
    top: -50px;
    left: 50%;
    width: 300px;
    text-align: center;
    transform: translate(-50%, 0);
    font-size: 24px;
    color: #000;
    font-weight: bold;
  }
  .circle-container {
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 300px;
    height: 300px;
    background: orange;
    border-radius: 50%;
    border: 10px solid orange;
    box-shadow: 0 0 0 20px rgb(255, 187, 0);
    overflow: hidden;
    .prize-container{
      width: calc(100% - 20px);
      height: calc(100% - 20px);
      background: #fff;
      position: absolute;
      left: 50%;
      top: calc(-50% + 20px);
      transform-origin: 0% 100%;
      &:nth-child(2n-1){
        background: rgb(255, 220, 144);
      }

      &.odd-color{
        background: rgb(255, 208, 208);
      
      }

      &.two-prize{
        height: calc(100%);
        transform-origin: 0% 50%;
        top: calc(0%);
      }

      &.one-prize{
        height: calc(100%);
        width: calc(100%);
        transform-origin: 50% 50%;
        top: 0;
        left: 0;
        background: #fff;
      }
    }
    .prize{
      width: calc(100% - 20px);
      height: calc(100% - 20px);
      position: absolute;
      top: calc(-50% + 20px);
      transform-origin: 50% 100%;
      .prize-child{
        width: 50%;
        height: 50%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        color: #000;
        font-size: 16px;
        position: absolute;
        bottom: 15%;
        left: 50%;
        transform: translate(-50%, 0);
        // background: orange;
        >div{
          font-size: 18px;
          font-weight: bold;
          color: orange;
        }
        >p{
          font-size: 14px;
          font-weight: bold;
          padding: 0;
          margin: 0;
          height: 50px;
          writing-mode: vertical-lr
        }
        
      }
    }
  }
  .run-btn{
    position: absolute;
    z-index: 10;
    width: 90px;
    height: 90px;
    border-radius: 50%;
    background: orange;
    color: #fff;
    font-size: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    border: 0;
    >span{
      position: absolute;
      border-top: 60px solid transparent;
      border-left: 40px solid transparent;
      border-right: 40px solid transparent;
      border-bottom: 60px solid orange;
      z-index: 0;
      top: calc(-100% - 0px);
    }
    &:hover{
      background: rgb(255, 187, 0);
      >span{
        border-bottom: 60px solid rgb(255, 187, 0);
      }
    }
  }
  .light-div{
    position: absolute;
    width: calc(300px + 60px);
    height: calc(300px + 60px);
    // background: red;
    border-radius: 50%;
    z-index: 10;
    >div{
      position: absolute;
      width: 12px;
      height: 12px;
      border-radius: 50%;
      top: 4px;
      left: 50%;
      transform: translate(-50%, 0);
      animation: light 1s infinite;
      z-index: 10;
    }
    &:nth-child(2n)>div{
      animation: lightShining 1s infinite;
      // animation-delay: 125ms;
    }
    &:nth-child(2n-1)>div{
      animation: lightShining 1s infinite;
      animation-delay: 500ms;
    }
  }
}


</style>
