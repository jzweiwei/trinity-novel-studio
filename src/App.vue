<template>
  <n-config-provider :locale="zhCN" :date-locale="dateZhCN">
    <n-message-provider>
      <h1 style="text-align:center; margin:20px 0">三位一体·商业网文创作系统 v2.0</h1>
      <n-tabs type="card">
        <n-tab-pane name="workflow" tab="宏观十一工作流">
          <WorkflowStep />
        </n-tab-pane>
        <n-tab-pane name="opening" tab="黄金开篇生成器">
          <OpeningGenerator />
        </n-tab-pane>
        <n-tab-pane name="settings" tab="模型设置">
          <ModelSettings />
        </n-tab-pane>
      </n-tabs>
    </n-message-provider>
  </n-config-provider>
</template>

<script setup>
import { zhCN, dateZhCN } from 'naive-ui'
import WorkflowStep from './components/WorkflowStep.vue'
import OpeningGenerator from './components/OpeningGenerator.vue'
import ModelSettings from './components/ModelSettings.vue'
</script>