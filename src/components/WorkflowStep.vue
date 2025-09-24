<template>
  <n-card title="第一步：一句话概括">
    <p>请用一句话告诉我，这是一个关于“谁”，在“什么情况下”，通过“什么方式”，达成了“什么结果”的故事？</p>
    <n-input v-model:value="input" type="textarea" :rows="3" placeholder="例如：灵根平庸的山村少年韩立..." />
    <n-button @click="generate" :loading="loading" style="margin-top:10px">AI 生成</n-button>
    <n-alert v-if="output" title="AI 建议" type="info" style="margin-top:15px">{{ output }}</n-alert>
  </n-card>
</template>

<script setup>
import { ref } from 'vue'

const input = ref('')
const output = ref('')
const loading = ref(false)

const generate = async () => {
  const config = JSON.parse(localStorage.getItem('aiModelConfig') || '{}')
  if (!config.apiKey) {
    window.$message.error('请先在【模型设置】中填写 API Key')
    return
  }

  loading.value = true
  try {
    const prompt = `你是一个顶级商业网文策划师。请根据以下想法，生成一句符合“三位一体·商业网文创作系统 v2.0”规则的一句话概括（25字以内，含动词、困境、结果）：${input.value}`
    
    let url = '', headers = {}, body = {}
    if (config.provider === 'qwen') {
      url = 'https://dashscope.aliyuncs.com/api/v1/services/aigc/text-generation/generation'
      headers = { 'Authorization': `Bearer ${config.apiKey}`, 'Content-Type': 'application/json' }
      body = { model: config.model, input: { messages: [{ role: 'user', content: prompt }] } }
    } else {
      throw new Error('当前仅支持 Qwen，其他模型需扩展')
    }

    const res = await fetch(url, { method: 'POST', headers, body: JSON.stringify(body) })
    const data = await res.json()
    let text = data.output?.choices?.[0]?.message?.content || '生成失败'

    // 🔥 严格遵守 2.0.txt 元指令
    text = text
      .replace(/[a-zA-Z]/g, '')          // 语言绝对纯净
      .replace(/……|…/g, '...')           // 省略号标准化
      .replace(/\*/g, '')                // 禁用星号
      .replace(/~/g, '\\~')              // 波浪线转义
      .trim()

    output.value = text
  } catch (e) {
    window.$message.error(e.message || '调用失败')
  }
  loading.value = false
}
</script>