<script setup>
import {onMounted, ref} from 'vue'

const props = defineProps(['urls'])

// 创建一个响应式的 `urls` 变量，并将 `props.urls` 的数据复制到其中
const localUrls = ref(props.urls.map(url => ({...url, status: 'none'})))

// 输出urls
// console.log(JSON.stringify(props.urls))
// example: [{"title":"qb-pb","url":"https://qb.pb.endaosi.com:8927/", "status":"none"}]


onMounted(() => {
  // 开始请求url的/stat路径，并修改状态为checking，然后在得到200或者请求失败后改为failed
  localUrls.value.forEach(group => {
    group.branches.forEach(url => {
      url.status = 'checking'
      url.startTime = performance.now();
      fetch(url.check.replace(/\/$/, '') + '?t=' + url.startTime).then(res => {
        if (res.status === 200) {
          url.status = 'success'
          url.endTime = performance.now();
          url.responseTime = Math.round(url.endTime - url.startTime)
        } else {
          console.log(res)
          url.status = 'failed'
          url.error = res.status
        }
      }).catch((e) => {
        url.status = 'failed'
        url.error = e
        // console.log(e.message) // Failed to fetch
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

  p {
    margin-top: 0.5rem;
    font-size: .9rem;
    color: #797979;
  }
}

</style>
<template>
  <div class="items">
    <div v-for="group in localUrls" class="item">
      <div class="block">
        <div class="title">
          <img class="icon" :src="group.icon"/>
          <h2>{{ group.title }}</h2>
          <p>{{ group.desc }}</p>
        </div>
        <div class="lines">
          <div v-for="url in group.branches" class="line">
            <div>
              {{ url.index }}:
              <a :href="url.target" target="blank">
                {{ url.title }}
              </a>
            </div>
            <div style="margin: 3px 0 5px;">
              <span>{{ url.status }}: </span>
              <span v-if="url.status==='success'" style="color: green;">
                  {{ url.responseTime }}ms
                </span>
              <span v-if="url.status==='failed'" style="color: red;">
                  {{ url.error }}
                </span>
            </div>

          </div>
        </div>
      </div>
    </div>
  </div>
</template>
