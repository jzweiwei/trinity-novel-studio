<template>
  <n-card title="大模型 API 设置">
    <n-form :model="config" label-placement="left" label-width="120px">
      <n-form-item label="模型提供商">
        <n-select v-model:value="config.provider" :options="providers" />
      </n-form-item>
      <n-form-item label="API Key">
        <n-input v-model:value="config.apiKey" type="password" show-password-on="click" />
      </n-form-item>
      <n-form-item label="模型名称">
        <n-input v-model:value="config.model" placeholder="如 qwen-max" />
      </n-form-item>
      <n-button @click="save" type="primary">保存</n-button>
    </n-form>
  </n-card>
</template>

<script setup>
import { reactive } from 'vue'

const providers = [
  { label: '通义千问 (Qwen)', value: 'qwen' },
  { label: 'DeepSeek', value: 'deepseek' },
  { label: 'Kimi', value: 'kimi' },
  { label: 'OpenAI', value: 'openai' }
]

const config = reactive({
  provider: 'qwen',
  apiKey: '',
  model: 'qwen-max'
})

// 从 localStorage 加载
const saved = localStorage.getItem('aiModelConfig')
if (saved) {
  try {
    Object.assign(config, JSON.parse(saved))
  } catch {}
}

const save = () => {
  localStorage.setItem('aiModelConfig', JSON.stringify(config))
  window.$message.success('已保存')
}
</script>