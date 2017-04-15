<template>
<div class="quillWrapper">
  <div ref="quillContainer" :id="id"></div>
</div>
</template>
<script>
import Quill from 'quill'
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'

var defaultToolbar = [
  ['bold', 'italic', 'underline', 'strike'],
  ['blockquote', 'code-block', 'image'],

  [{ 'list': 'ordered'}, { 'list': 'bullet' }],

  [{ 'indent': '-1'}, { 'indent': '+1' }],
  [{ 'header': [1, 2, 3, 4, 5, 6, false] }],

  [{ 'color': [] }, { 'background': [] }],
  [{ 'font': [] }],
  [{ 'align': [] }],

  ['clean']
]

export default {
  name: 'vue-editor',
  props: {
    value: String,
    id: {
      type: String,
      default: 'quill-container'
    },
    placeholder: String,
    disabled: Boolean,
    editorToolbar: Array,
    plugins: Array
  },

  data() {
    return {
      quill: null,
      editor: null,      
      modules: {
        toolbar: this.editorToolbar ? this.editorToolbar : defaultToolbar
      }      
    }
  },

  mounted() {
    this.initializeVue2Editor()
    this.handleUpdatedEditor()
  },

  watch: {
    value (val) {
      if (val !=  this.editor.innerHTML && !this.quill.hasFocus()) {
        this.editor.innerHTML = val
      }
    },
    disabled(status) {
      this.quill.enable(!status);
    }
  },

  methods: {
    initializeVue2Editor() {
      this.prepareModules()
      this.setQuillElement()
      this.setEditorElement()
      this.checkForInitialContent()
    },

    prepareModules() {      
      if(this.plugins !== undefined)
      {          
          let self = this
          this.plugins.forEach(function(element, index){ 
            Quill.register('modules/' + element.alias, element.module)
            self.modules[element.alias] = element.config
          })
      }
    },

    setQuillElement() {
      this.quill = new Quill(this.$refs.quillContainer, {
        modules: this.modules,
        placeholder: this.placeholder ? this.placeholder : '',
        theme: 'snow',
        readOnly: this.disabled ? this.disabled : false,
      })
    },

    setEditorElement() {
      this.editor = document.querySelector(`#${this.id} .ql-editor`)
    },

    checkForInitialContent() {
      this.editor.innerHTML = this.value || ''
    },

    handleUpdatedEditor() {
      this.quill.on('text-change', () => {
        this.$emit('input', this.editor.innerHTML)
      })
    }
  }
}
</script>

<style>
  #quill-container {
    height: 400px;
  }
</style>
