<template>
  <n-layout-header bordered class="nav" :style="style">
    <n-text tag="div" class="ui-logo" :depth="1" @click="handleLogoClick">
      <span>MapleStoryTools For LMS </span><small>(v0.0.1)</small>
    </n-text>
    <div
        :style="
        !isMobile ? 'display: flex; align-items: center; overflow: hidden;' : ''
      "
    >
      <div v-if="!(isMobile || isTablet)" class="nav-menu">
        <n-menu
            ref="menuInstRef"
            responsive
            mode="horizontal"
            :options="menuOptions"
            :render-label="renderMenuLabel"
        />
      </div>
    </div>
    <n-popover
        v-if="isMobile || isTablet"
        ref="mobilePopoverRef"
        style="padding: 0; width: 288px"
        placement="bottom-end"
        display-directive="show"
        trigger="click"
    >
      <template #trigger>
        <n-icon size="20" style="margin-left: 12px">
          <menu-outline />
        </n-icon>
      </template>
      <div style="overflow: auto; max-height: 79vh">
        <n-menu
            :options="mobileMenuOptions"
            :indent="18"
            :render-label="renderMenuLabel"
            @update:value="handleUpdateMobileMenu"
        />
      </div>
    </n-popover>
    <div v-else class="nav-end">
      <n-text class="credit-text" :depth="3">by Ngxx, forked from lintx</n-text>
      <n-button
          size="small"
          tag="a"
          quaternary
          class="nav-picker"
          :href="qqGroupUrl"
          target="_blank"
      >
        Play LMS
      </n-button>
      <n-button
          size="small"
          quaternary
          class="nav-picker"
          @click="handleThemeUpdate"
      >
        {{ themeLabelMap[themeName] }}
      </n-button>
    </div>
  </n-layout-header>
</template>


<script setup>
import { useIsMobile, useIsTablet } from '../utils/composables'
import { useTheme, useThemeName } from "@/assets/store.js";
import { MenuOutline } from '@vicons/ionicons5'
import { useRouter, useRoute, RouterLink } from 'vue-router'
import { h } from 'vue'

const route = useRoute()
const router = useRouter()

const isMobile = useIsMobile()
const isTablet = useIsTablet()
const style = computed(() => {
  return isMobile.value
      ? {
        '--side-padding': '16px',
        'grid-template-columns': 'auto 1fr auto'
      }
      : {
        '--side-padding': '32px',
        'grid-template-columns': 'calc(340px - var(--side-padding)) 1fr auto' // 增加到 340px
      }
})

function handleLogoClick () {
  router.push('/')
}

const menuOptions = router.options.routes.map(r => {
  return { key: r.name, label: r.meta.label, path: r.path, sort: r.meta.sort }
}).sort((a, b) => a.sort - b.sort)

const theme = useTheme()
const themeName = useThemeName()
function handleThemeUpdate () {
  if (themeName.value === 'dark') {
    themeName.value = 'light'
  } else {
    themeName.value = 'dark'
  }
  localStorage.setItem("tools_theme",themeName.value)
}
const themeLabelMap = computed(() => ({
  dark: '暗色',
  light: '浅色'
}))
//const repoUrl = "https://github.com/zhangchizh/MapleStoryTools"
const qqGroupUrl = "https://qm.qq.com/q/xncfJuFqZq" // 替换为实际 QQ 群密钥

const mobilePopoverRef = ref(null)
const mobileMenuOptions = computed(() => {
  return [
    {
      key: 'theme',
      label: themeLabelMap.value[themeName.value]
    },
    ...menuOptions,
    {
      key: 'Play LMS',
      label: 'Play LMS'
    }
  ]
})
function handleUpdateMobileMenu (value, { path }) {
  if (value === 'theme') {
    handleThemeUpdate()
  } else if (path) {
    router.push(path)
  } else {
    window.open(qqGroupUrl, '_blank')
  }
  mobilePopoverRef.value.setShow(false)
}

const renderMenuLabel = (option) => {
  if (!('path' in option)) {
    return option.label
  }
  return h(
      RouterLink,
      {
        to: option.path
      },
      { default: () => option.label }
  )
}
</script>

<style scoped>
.nav {
  display: grid;
  grid-template-rows: calc(var(--header-height) - 1px);
  align-items: center;
  padding: 0 var(--side-padding);
}

.ui-logo {
  cursor: pointer;
  display: flex;
  align-items: center;
  font-size: 22px;
  white-space: nowrap;
}

.ui-logo > img {
  margin-right: 12px;
  height: 32px;
  width: 32px;
}

.ui-logo > small {
  font-size: 12px;
  margin-left: 8px;
}

.lms-link {
  color: inherit; /* 继承主题颜色 */
  text-decoration: underline; /* 添加下划线以示链接 */
  cursor: pointer;
}

.lms-link:hover {
  color: var(--primary-color); /* Naive UI 主色，鼠标悬停时 */
}

.nav-menu {
  padding-left: 36px;
  overflow: hidden;
  flex-grow: 0;
  flex-shrink: 1;
}

.nav-picker {
  margin-right: 4px;
}

.nav-picker.padded {
  padding: 0 10px;
}

.nav-picker:last-child {
  margin-right: 0;
}

.nav-end {
  display: flex;
  align-items: center;
}

.credit-text {
  font-size: 12px;
  margin-right: 12px;
  white-space: nowrap;
}
</style>

<style>
.nav-menu .n-menu-item,
.nav-menu .n-submenu,
.nav-menu .n-menu-item-content {
  height: calc(var(--header-height) - 1px) !important;
}
</style>