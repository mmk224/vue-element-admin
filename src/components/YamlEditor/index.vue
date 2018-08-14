<template>
  <div class="yaml-editor">
    <div v-if="header" class="header">
      <span>{{readOnly ? 'ReadOnly' : 'Writable'}}</span>
      <span v-if="allowExport"
            @click="exportFile"
            class="yaml-editor-button">Export</span>
    </div>
    <textarea ref="textarea"></textarea>
  </div>
</template>

<script>
import CodeMirror from 'codemirror'
import 'codemirror/addon/fold/foldgutter.css'
import 'codemirror/addon/lint/lint.css'
import 'codemirror/addon/scroll/simplescrollbars.css'
import 'codemirror/lib/codemirror.css'
import 'codemirror/theme/material.css'

// code extensions
import 'codemirror/mode/yaml/yaml.js'

// addons
import 'codemirror/addon/fold/foldcode.js'
import 'codemirror/addon/fold/foldgutter.js'
import 'codemirror/addon/fold/indent-fold.js'
import 'codemirror/addon/lint/lint.js'
import 'codemirror/addon/scroll/simplescrollbars'

import * as FileSaver from 'file-saver'

export default {
  name: 'yamlEditor',
  data() {
    return {
      yamlEditor: false,
      config: null
    }
  },
  props: {
    value: {
      type: String,
      default: ''
    },
    mode: {
      type: String,
      default: 'yaml'
    },
    theme: {
      type: String,
      default: 'material'
    },
    lint: {
      type: Boolean,
      default: true
    },
    readOnly: {
      type: Boolean,
      default: false
    },
    gutters: {
      type: Array,
      default: function() {
        return ['CodeMirror-lint-markers']
      }
    },
    lineNumbers: {
      type: Boolean,
      default: true
    },
    header: {
      type: Boolean,
      default: true
    },
    allowExport: {
      type: Boolean,
      default: true
    },
    exportFileName: {
      type: String,
      default: 'file'
    },
    exportFileExt: {
      type: String,
      default: 'yaml'
    }
  },
  watch: {
    value(value) {
      const editor_value = this.yamlEditor.getValue()
      if (value !== editor_value) {
        this.yamlEditor.setValue(this.value)
      }
    }
  },
  mounted() {
    this.config = {
      readOnly: this.readOnly,
      mode: this.mode,
      theme: this.theme,
      lint: this.lint,
      lineNumbers: this.lineNumbers,
      gutters: this.gutters,
      scrollbarStyle: 'simple'
    }
    this.yamlEditor = CodeMirror.fromTextArea(this.$refs.textarea, this.config)

    this.yamlEditor.setValue(this.value)
    this.yamlEditor.on('change', cm => {
      this.$emit('changed', cm.getValue())
      this.$emit('input', cm.getValue())
    })
  },
  methods: {
    getValue() {
      return this.yamlEditor.getValue()
    },
    exportFile() {
      const blob = new Blob([this.value])
      FileSaver.saveAs(blob, `${this.exportFileName}.${this.exportFileExt}`)
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
  .yaml-editor {
    .header {
      width:100%;
      display: flex;
      align-items: center;
      background-color: #333;
      height: 40px;
      padding: 0 16px;
      > span {
        margin-right: 16px;
        color: #16b9fe;
        font-weight: 600;
        &.yaml-editor-button {
          cursor: pointer;
          background-color: transparent;
          border: none;
          color: #999;
          margin-left: 12px;
          transition: color ease 0.1s;
          &:focus {
            outline: none;
          }
          &:hover {
            color: #fff;
          }
        }
      }
    }
  }
</style>
<style scoped>
.yaml-editor{
  height: 100%;
  position: relative;
}
.yaml-editor >>> .CodeMirror {
  height: auto;
  min-height: 300px;
}
.yaml-editor >>> .CodeMirror-scroll{
  min-height: 300px;
}
</style>
