<script setup>
import {onMounted, ref} from 'vue'

const props = defineProps(['urls'])

// 创建一个响应式的 `urls` 变量，并将 `props.urls` 的数据复制到其中
const localUrls = ref(props.urls.map(url => ({ ...url, status: 'none' })))

// 输出urls
// console.log(JSON.stringify(props.urls))
// example: [{"title":"qb-pb","url":"https://qb.pb.endaosi.com:8927/", "status":"none"}]


onMounted(() => {
  // 开始请求url的/stat路径，并修改状态为checking，然后在得到200或者请求失败后改为failed
  localUrls.value.forEach(group => {
    group.branches.forEach(url => {
      url.status = 'checking'
      url.startTime = performance.now();
      fetch(url.url.replace(/\/$/, '') + '/stat?t=' + url.startTime).then(res => {
        if (res.status === 200) {
          url.status = 'success'
          url.endTime = performance.now();
          url.responseTime = Math.round(url.endTime - url.startTime)
        } else {
          url.status = 'failed'
          url.error = res.status
        }
      }).catch((e) => {
        url.status = 'failed'
        url.error = e
      })
    })
  })
})
</script>
<style scoped>


.items {
  display: grid;
  grid-template-columns: 1fr;
  gap: .625rem;
}
@media (min-width: 1024px) {
  .items {
    grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
  }
}

.item {
  align-items: center;

  border: 1px solid var(--color-border);
  background: var(--color-background-soft);
}

.block {
  padding: 24px;
  display: block;
  border: 1px solid var(--color-border);
  background-color: var(--color-background);
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
}
.lines {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
  gap: .625rem;
  margin-top: 12px;
}

.line {
  display: block;
  border-radius: .5rem;
  color: inherit;
  text-decoration: inherit;
  border: 1px solid transparent;
  white-space: nowrap;
  padding: 6px 12px;
  background-color: hsla(0, 0%, 72%, 0.1);
}
.line:hover {
  background-color: hsla(160, 100%, 37%, 0.2);
}


.title {
  h2 {
    font-size: 2rem;
    display: inline-block;
    margin-left: .5rem;
  }
  .icon {
    display: inline-block;
    transition: background-color 0.25s;
    width: 3.2em;
    vertical-align: bottom;
  }
}

</style>
<template>
    <div class="items">
      <div v-for="group in localUrls" class="item">
        <div class="block">
            <div class="title">
              <img class="icon" :src="group.icon" />
              <h2>{{ group.title }}</h2>
            </div>
            <div class="lines">
              <a v-for="url in group.branches" class="line" :href="url.url">
                <div style="font-weight: bold;">🔗 {{ url.title }}</div>
                <div>status: {{url.status}}</div>
                <div style="color: green;" v-if="url.status==='success'">{{url.responseTime}} ms</div>
                <div style="color: red;" v-if="url.status==='failed'">{{url.error}}</div>
              </a>
            </div>
        </div>
      </div>
    </div>
</template>
