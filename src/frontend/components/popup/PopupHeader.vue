<script setup lang="ts">
import { computed } from 'vue'
import ThemeIcon from '../common/ThemeIcon.vue'
import type { McpRequest } from '../../types/popup'

interface Props {
  currentTheme?: string
  loading?: boolean
  showMainLayout?: boolean
  alwaysOnTop?: boolean
  mcpRequest?: McpRequest | null
}

interface Emits {
  themeChange: [theme: string]
  openMainLayout: []
  toggleAlwaysOnTop: []
}

const props = withDefaults(defineProps<Props>(), {
  currentTheme: 'dark',
  loading: false,
  showMainLayout: false,
  alwaysOnTop: false,
  mcpRequest: null,
})

const emit = defineEmits<Emits>()

// 计算标题文本
const titleText = computed(() => {
  const parts: string[] = []

  // 优先显示 workspace 名称
  if (props.mcpRequest?.workspace_name) {
    parts.push(props.mcpRequest.workspace_name)
  }

  // 显示客户端名称
  if (props.mcpRequest?.client_name) {
    parts.push(props.mcpRequest.client_name)
  }

  // 如果有任何信息，返回组合后的标题
  if (parts.length > 0) {
    return parts.join(' - ')
  }

  // 如果没有任何信息，使用默认标题
  return '寸止 - 告别AI提前终止烦恼，助力AI更加持久'
})

function handleThemeChange() {
  // 切换到下一个主题
  const nextTheme = props.currentTheme === 'light' ? 'dark' : 'light'
  emit('themeChange', nextTheme)
}

function handleOpenMainLayout() {
  emit('openMainLayout')
}

function handleToggleAlwaysOnTop() {
  emit('toggleAlwaysOnTop')
}
</script>

<template>
  <div class="px-4 py-3 select-none">
    <div class="flex items-center justify-between">
      <!-- 左侧：标题 -->
      <div class="flex items-center gap-3">
        <div class="w-3 h-3 rounded-full bg-primary-500" />
        <h1 class="text-base font-medium text-white">
          {{ titleText }}
        </h1>
      </div>

      <!-- 右侧：操作按钮 -->
      <n-space size="small">
        <!-- 置顶按钮 -->
        <n-button
          size="small"
          quaternary
          circle
          :title="props.alwaysOnTop ? '取消置顶' : '窗口置顶'"
          @click="handleToggleAlwaysOnTop"
        >
          <template #icon>
            <div
              :class="props.alwaysOnTop ? 'i-carbon-pin-filled' : 'i-carbon-pin'"
              class="w-4 h-4 text-white"
            />
          </template>
        </n-button>
        <n-button
          size="small"
          quaternary
          circle
          :title="props.showMainLayout ? '返回聊天' : '打开设置'"
          @click="handleOpenMainLayout"
        >
          <template #icon>
            <div
              :class="props.showMainLayout ? 'i-carbon-chat' : 'i-carbon-settings'"
              class="w-4 h-4 text-white"
            />
          </template>
        </n-button>
        <n-button
          size="small"
          quaternary
          circle
          :title="`切换到${props.currentTheme === 'light' ? '深色' : '浅色'}主题`"
          @click="handleThemeChange"
        >
          <template #icon>
            <ThemeIcon :theme="props.currentTheme" class="w-4 h-4 text-white" />
          </template>
        </n-button>
      </n-space>
    </div>
  </div>
</template>
