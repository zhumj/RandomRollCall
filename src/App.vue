<template>
  <div style="display: flex; justify-content: center;">
    <el-card style="min-width: 50%;">
      <div style="display: flex; flex-direction: column; justify-content: center; align-items: center;">
        <img alt="Vue logo" src="./assets/logo_random_roll_call.png" style="width: 256px;">
        <p style="font-size: 24px;">{{ selectedName }}</p>
        <div style="width: 100%; display: flex; justify-content: center; margin-top: 24px;">
          <el-button type="primary" :disabled="startDisabled" @click="onStart" style="width: 20%; margin: 0;">开始</el-button>
          <div style="width: 24px;"></div>
          <el-button type="primary" :disabled="startDisabled" @click="onRecover" style="width: 20%; margin: 0;">恢复</el-button>
        </div>
        <div style="width: 100%; display: flex; flex-direction: column; margin-top: 24px; align-items: flex-start;">
          <div style="font-size: 18px; margin-bottom: 16px;">选中名单</div>
          <el-input v-model="selectedDatasStr" type="textarea" placeholder="选中名单" resize="none" readonly disabled :autosize="{ minRows: 2, maxRows: 4 }" input-style="color: red;"></el-input>
        </div>
        <div style="width: 100%; display: flex; flex-direction: column; margin-top: 24px; align-items: flex-start;">
          <div style="font-size: 18px; margin-bottom: 16px;">抽选名单<label style="font-size: 16px; color:deeppink;">(多个选项用英文","隔开)</label></div>
          <el-input type="textarea" :autosize="{ minRows: 2, maxRows: 4 }" resize="none" v-model.trim="rawDatas" placeholder="请输入抽选名单"></el-input>
        </div>
        <div style="width: 100%; display: flex; flex-direction: column; margin-top: 24px; align-items: flex-start;">
          <div style="font-size: 18px;">抽选数量</div>
          <el-input v-model.number="selectCount" type="number" :min="1" placeholder="请输入抽选数量" oninput="(value.trim() === '' || value === '0') ? value = 1 : value = value.replace(/[^0-9]/g,'')" style="margin-top: 16px;"></el-input>
        </div>
      </div>
    </el-card>
    <el-dialog v-model="showDialog" width="30%" top="15%" :show-close="false" :center="true" :destroy-on-close="true">
      <template #header>
        <div style="display: flex; justify-content: center; align-items: center;">
          <img src="./assets/salute.png" style="width: 48px; height: 48px;">
          <p style="font-size: 36px; margin: 0;">恭喜</p>
          <img src="./assets/salute.png" style="width: 48px; height: 48px; transform: rotateY(180deg);">
        </div>
      </template>
      <div style="text-align: center; font-size: 18px;">恭喜 <label style="font-size: 30px; color: red;">{{ selectedDatasStr }}</label> 被选中！</div>
    </el-dialog>
  </div>
</template>

<script lang="ts" setup>
import { ref } from "vue"
import 'element-plus/es/components/message/style/css'
import { ElMessage } from 'element-plus'

const rawDatas = ref<string>('中森明菜,周慧敏,克莉丝汀·斯图尔特,西野翔,洪真英,张敏,桥本环奈,邱淑贞,奥黛丽·赫本,赵丽颖,星野悠月,布兰妮·斯皮尔斯,邓寓君,坂井泉水,秦可卿,秦羽墨')
const randomDatas = ref<string[]>([])
const selectedDatas = ref<string[]>([])
const selectedDatasStr = ref<string>('')
const selectedName = ref<string>('选中有奖哦')
const selectedIndex = ref<number>(-1)
const startDisabled = ref<boolean>(false)
const selectCount = ref<number>(1)
const showDialog = ref<boolean>(false)
let timer: number

const onRecover = () => {
  randomDatas.value.length = 0
  let datas = rawDatas.value.split(',')
  for (let index = 0; index < datas.length; index++) {
    const value = datas[index]
    if (value.length > 0) {
      randomDatas.value.push(value)
    }
  }
  selectedDatas.value.length = 0
  selectedDatasStr.value = ''
  selectedName.value = '选中有奖哦'
  selectedIndex.value = -1
  startDisabled.value = false
}

const onStart = () => {
  onRecover()
  if (randomDatas.value.length < 1) {
    ElMessage.error('请设置抽选名单')
    return
  }
  if (selectCount.value >= randomDatas.value.length) {
    ElMessage.error('抽选数量不可大于或等于抽选名单数量')
    return
  }
  startDisabled.value = true
  startRandom()
}

const startRandom = () => {
  timer = setInterval(() => {
    selectedIndex.value = Math.floor(Math.random()*(randomDatas.value.length))
    selectedName.value = randomDatas.value[selectedIndex.value]
  }, 100)
  setTimeout(() => {
    clearInterval(timer)
    selectedDatas.value.push(selectedName.value)
    randomDatas.value.splice(selectedIndex.value, 1)
    selectedDatasStr.value = selectedDatas.value.join(', ')
    if (selectedDatas.value.length < selectCount.value) {
      setTimeout(() => {
        startRandom()
      }, 500)
    } else {
      setTimeout(() => {
        showDialog.value = true
        startDisabled.value = false
      }, 500)
    }
  }, 1500)
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
