<script setup>
import { ref, defineEmits } from 'vue'

const showDialog = ref(false)
const countdown = ref(0)
const targetUrl = ref('')
let interval = null

const emit = defineEmits(['cancel'])


const open = (url) => {
  targetUrl.value = url
  showDialog.value = true
  countdown.value = 5
  interval = setInterval(() => {
    countdown.value -= 1
    if (countdown.value === 1) {
      nowRedirect()
    }
  }, 1000)
}

const nowRedirect = () => {
  clearInterval(interval)
  console.log('跳转到：', targetUrl.value)
  location.href = targetUrl.value
}
const cancelRedirect = () => {
  showDialog.value = false
  clearInterval(interval)
  emit('cancel')
}

defineExpose({ open })
</script>

<template>
  <div v-if="showDialog" class="overlay">
    <div class="dialog">
      <p>即将跳转到最优线路：</p>
      <p>{{ targetUrl }}</p>
      <p>倒计时 {{ countdown }} 秒</p>
      <button @click="() => location.href = targetUrl" class="redirect-button">立即跳转</button>
      <button @click="cancelRedirect" class="cancel-button">取消跳转</button>
    </div>
  </div>
</template>

<style scoped>
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.dialog {
  background: rgba(255, 255, 255, 0.62);
  padding: 20px;
  border-radius: 8px;
  text-align: center;
}

.redirect-button, .cancel-button {
  margin: 10px;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.redirect-button {
  background-color: rgba(77, 138, 81, 0.54);
  color: #e0e0e0;
}

.cancel-button {
  background-color: rgba(91, 53, 51, 0.62);
  color: #b6b6b6;
}
</style>
