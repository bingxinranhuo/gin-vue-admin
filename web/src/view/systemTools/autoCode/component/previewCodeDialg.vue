<template>
  <div class="previewCode">
    <el-tabs v-model="activeName">
      <el-tab-pane v-for="(item, key) in previewCode" :key="key" :label="key" :name="key">
        <div :id="key" class="tab-info" />
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script setup>
import marked from 'marked'
import hljs from 'highlight.js'
import 'highlight.js/styles/atelier-plateau-light.css'
import { ElMessage } from 'element-plus'
import { defineProps, defineExpose, onMounted, ref } from 'vue'

const props = defineProps({
  previewCode: {
    type: Object,
    default() {
      return {}
    }
  }
})

const activeName = ref('')
onMounted(() => {
  marked.setOptions({
    renderer: new marked.Renderer(),
    highlight: function(code) {
      return hljs.highlightAuto(code).value
    },
    pedantic: false,
    gfm: true,
    tables: true,
    breaks: false,
    sanitize: false,
    smartLists: true,
    smartypants: false,
    xhtml: false
  })
  for (const key in props.previewCode) {
    if (activeName.value === '') {
      activeName.value = key
    }
    document.getElementById(key).innerHTML = marked(props.previewCode[key])
  }
})

const selectText = () => {
  const element = document.getElementById(activeName.value)
  if (document.body.createTextRange) {
    const range = document.body.createTextRange()
    range.moveToElementText(element)
    range.select()
  } else if (window.getSelection) {
    const selection = window.getSelection()
    const range = document.createRange()
    range.selectNodeContents(element)
    selection.removeAllRanges()
    selection.addRange(range)
  } else {
    alert('none')
  }
}
const copy = () => {
  selectText()
  document.execCommand('copy')
  ElMessage.success('复制成功')
}

defineExpose({ copy })

</script>

<script>

export default {

}
</script>

<style lang="scss">
.previewCode {
  .tab-info {
    height: 50vh;
    background: #fff;
    padding: 0 20px;
    overflow-y: scroll;
  }
}
</style>
