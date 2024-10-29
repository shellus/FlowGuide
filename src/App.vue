<script setup>
import urls from './urls.json'
import SiteGroup from "@/components/SiteGroup.vue";
import { isDark, SwitchIcon } from "vue-dark-switch"

console.log("当前黑暗模式：" + isDark.value)
</script>

<template>
  <div class="container">
    <header>
      <h1>FlowGuide</h1>
      <SwitchIcon />
    </header>
    <SiteGroup :urls="urls"></SiteGroup>
    <article style="margin-top: 20px;">
      <h2>地址类型和优先级</h2>
      <ul>
        <li>1. private 私有网络优先，安全，速度快</li>
        <li>2. public 公网端口映射，20Mb/s，免费流量</li>
        <li>3. frp 24Mb/s，收费流量</li>
      </ul>
    </article>
    <article style="margin-top: 20px;">
      <h2>说明</h2>
      <p>⭕原理：在浏览器中通过http请求尝试每一个预配置的地址，可以得知每个地址的连通性情况，然后你可以选择一个地址进行访问</p>
      <p>⏩状态：checking-正在检查，success-成功，failed-失败</p>
      <p>⚠️注意：通过http访问，无法检测内网网段，这是浏览器的安全限制。</p>
      <br>
      <p>❎自动跳转：使用参数`?auto={name}`会自动选择最快的地址跳转，未知的标记不会做任何动作；你可以将其加入收藏夹以实现静默自动化选择线路</p>
    </article>
    <footer>
      <p>本页面使用vue3构建，托管于阿里云OSS，仅作为学习调试用途。</p>
    </footer>
  </div>
</template>

<style scoped>
header, article, footer {
  margin-bottom: 20px;
  padding: 20px;
  background-color: rgb(113 113 113 / 15%);
}
</style>
