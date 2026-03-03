# Project Context: Vue 3 + TS + Tailwind App

## 🛠 技術棧規範 (Tech Stack)
- **Framework**: Vue 3 (使用 Composition API `<script setup>` 語法)
- **Language**: TypeScript (嚴格模式，禁止使用 `any`)
- **Styling**: Tailwind CSS
- **Icons**: Lucide Vue Next (預設圖示庫)
- **State Management**: Pinia (如果需要狀態管理)

## 🎨 代碼風格指南 (Coding Standards)
- **SFC 結構**: 遵循 `<script setup>` -> `<template>` -> `<style scoped>` 的順序。
- **TypeScript**: 
  - 優先使用 `interface` 定義 Props 與 Emits。
  - 使用 `defineProps<{ ... }>()` 和 `defineEmits<{ ... }>()`。
- **Tailwind**:
  - 優先使用 Tailwind 原生類名，除非必要不使用 `@apply`。
  - 響應式設計遵循移動端優先 (Mobile First) 原則。
- **命名規範**:
  - 元件檔名使用大駝峰 (PascalCase)，例如 `UserCard.vue`。
  - 變數與函式使用小駝峰 (camelCase)。

## 🧩 常用組件範例
當我要求建立新組件時，請參考以下基礎模板：
```vue
<script setup lang="ts">
interface Props {
  title: string
  isVisible?: boolean
}

withDefaults(defineProps<Props>(), {
  isVisible: true
})
</script>

<template>
  <div v-if="isVisible" class="p-4 rounded-lg bg-white shadow-md border border-slate-200">
    <h2 class="text-xl font-bold text-slate-800">{{ title }}</h2>
    <slot />
  </div>
</template>