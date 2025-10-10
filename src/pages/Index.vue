<script setup>
const router = useRouter()

const tools = router.options.routes.filter(r=>r.meta.showInNav!==false).map(r=>{return {...r.meta,path:r.path}}).sort((a,b)=>a.sort-b.sort)

const links = [
  {
    title: 'Link角色卡',
    url: 'https://www.neoska.me/link.html',
  },
  {
    title: '刷图规划工具-Maplemaps',
    url: 'https://maplemaps.net',
  },
]


defineOptions({
  route:{
    meta:{
      label:'首页',
      showInNav:false,
    },
    path:"/",
  }
})
</script>

<template>
  <n-space vertical>
    <n-grid x-gap="12" y-gap="12" :cols="4">
      <n-gi v-for="tool in tools">
        <n-card>
          <template #header>
            <router-link v-slot="{ navigate }" :to="tool.path" custom>
              <n-button text tag="a" type="primary" @click="navigate">{{tool.title}}</n-button>
            </router-link>
          </template>
          <template #cover>
            <n-icon :component="tool.icon" size="80" />
          </template>
          {{tool.desc}}
        </n-card>
      </n-gi>
    </n-grid>
    <n-thing>其他工具</n-thing>
    <n-grid x-gap="12" y-gap="12" :cols="4">
      <n-gi v-for="link in links">
        <n-card>
          <n-button text tag="a" :href="link.url" target="_blank" type="primary">{{link.title}}</n-button>
        </n-card>
      </n-gi>
    </n-grid>
  </n-space>
</template>

<style scoped>

</style>