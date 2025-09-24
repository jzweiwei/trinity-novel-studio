<template>
  <n-card title="黄金开篇生成器">
    <n-input v-model:value="oneSentence" placeholder="一句话概括" />
    <n-input v-model:value="chars" type="textarea" :rows="4" placeholder="主角与关键人物设定" style="margin-top:10px" />
    <n-button @click="generate" :loading="loading" type="primary" style="margin-top:10px">生成前三章</n-button>
    <div v-if="result" style="margin-top:20px; white-space: pre-wrap; background:#fff; padding:15px; border-radius:6px; font-family: sans-serif;">
      {{ result }}
    </div>
  </n-card>
</template>

<script setup>
import { ref } from 'vue'

const oneSentence = ref('')
const chars = ref('')
const result = ref('')
const loading = ref(false)

const generate = async () => {
  const config = JSON.parse(localStorage.getItem('aiModelConfig') || '{}')
  if (!config.apiKey) {
    window.$message.error('请先设置 API Key')
    return
  }

  loading.value = true
  try {
    const prompt = `你正在创作一部商业男频爽文。请严格遵守：
1. 仅输出简体中文，禁用任何英文字母。
2. 省略号统一为"..."，禁用"*"，"~"转义为"\\~"。
3. 第一章：动态切入，核心冲突前置，出场≤3人。
4. 第二章：主角危机。
5. 第三章：展现金手指，完成首次打脸，每章结尾设钩子。
6. 描写极致感官细节。

一句话：${oneSentence.value}
人物：${chars.value}

生成前三章，每章约800字。`

    let url = '', headers = {}, body = {}
    if (config.provider === 'qwen') {
      url = 'https://dashscope.aliyuncs.com/api/v1/services/aigc/text-generation/generation'
      headers = { 'Authorization': `Bearer ${config.apiKey}`, 'Content-Type': 'application/json' }
      body = { model: config.model, input: { messages: [{ role: 'user', content: prompt }] } }
    }

    const res = await fetch(url, { method: 'POST', headers, body: JSON.stringify(body) })
    const data = await res.json()
    let text = data.output?.choices?.[0]?.message?.content || '生成失败'

    // 🔥 再次执行元指令净化
    text = text
      .replace(/[a-zA-Z]/g, '')
      .replace(/……|…/g, '...')
      .replace(/\*/g, '')
      .replace(/~/g, '\\~')
      .trim()

    result.value = text
  } catch (e) {
    window.$message.error(e.message || '生成失败')
  }
  loading.value = false
}
</script>