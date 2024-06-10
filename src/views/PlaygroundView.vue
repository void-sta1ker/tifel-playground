<script>
import { defineComponent, ref, watch, shallowRef } from 'vue'
import { Codemirror } from 'vue-codemirror'
import { javascript } from '@codemirror/lang-javascript'
import { oneDark } from '@codemirror/theme-one-dark'
import Prism from 'vue-prism-component'
import tifel from 'tifel'

export default defineComponent({
  components: {
    Codemirror,
    Prism
  },
  setup() {
    const code = ref(`const a = true ? 1 : 0;\nconsole.log(a);\n`)
    const output = ref(tifel(code.value))
    const extensions = [javascript(), oneDark]

    // Watch for changes in the code ref and update output
    watch(code, (newCode) => {
      try {
        output.value = tifel(newCode)
      } catch {
        output.value = 'Error'
      }
    })

    // Codemirror EditorView instance ref
    const view = shallowRef()
    const handleReady = (payload) => {
      view.value = payload.view
    }

    // Status is available at all times via Codemirror EditorView
    const getCodemirrorStates = () => {
      const state = view.value.state
      const ranges = state.selection.ranges
      const selected = ranges.reduce((r, range) => r + range.to - range.from, 0)
      const cursor = ranges[0].anchor
      const length = state.doc.length
      const lines = state.doc.lines
      // more state info ...
      // return ...
    }

    return {
      code,
      output,
      extensions,
      handleReady,
      log: console.log
    }
  }
})
</script>

<template>
  <codemirror
    v-model="code"
    placeholder=""
    :style="{ height: '500px', width: '500px', marginRight: '20px' }"
    :autofocus="true"
    :indent-with-tab="true"
    :tab-size="2"
    :extensions="extensions"
    @ready="handleReady"
    @change="log('change', $event)"
    @focus="log('focus', $event)"
    @blur="log('blur', $event)"
  />

  <prism
    :key="output"
    language="javascript"
    style="border-radius: 0; height: 500px; margin: 0; padding-top: 4px"
    >{{ output }}
  </prism>
</template>

<style scoped>
@media (min-width: 1024px) {
}
</style>
