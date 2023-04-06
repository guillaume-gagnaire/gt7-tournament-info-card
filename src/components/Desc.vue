<template>
  <editor-content class="desc-editor" :editor="editor" />
</template>

<script>
import Document from '@tiptap/extension-document'
import StarterKit from '@tiptap/starter-kit'
import { Editor, EditorContent } from '@tiptap/vue-3'

const CustomDocument = Document.extend({
  content: 'heading list'
})

export default {
  props: {
    modelValue: String
  },
  components: {
    EditorContent
  },

  data () {
    return {
      editor: null
    }
  },

  mounted () {
    this.editor = new Editor({
      onUpdate: ({ editor }) => {
        this.$emit('update:modelValue', editor.getHTML())
      },
      extensions: [
        CustomDocument,
        StarterKit.configure({
          document: false
        })
      ],
      content: this.modelValue
    })
  },

  beforeUnmount () {
    this.editor.destroy()
  }
}
</script>

<style lang="scss">
/* Basic editor styles */
.ProseMirror {
  &:focus {
    outline: none;
  }

  > * + * {
    margin-top: 0.75em;
  }

  ul,
  ol {
    padding: 0 1rem;
  }

  h1,
  h2,
  h3,
  h4,
  h5,
  h6 {
    line-height: 1.1;
  }

  h1 {
    font-size: 22px;
  }

  li {
    font-size: 16px;
  }

  code {
    background-color: rgba(#616161, 0.1);
    color: #616161;
  }

  pre {
    background: #0d0d0d;
    color: #fff;
    font-family: 'JetBrainsMono', monospace;
    padding: 0.75rem 1rem;
    border-radius: 0.5rem;

    code {
      color: inherit;
      padding: 0;
      background: none;
      font-size: 0.8rem;
    }
  }

  img {
    max-width: 100%;
    height: auto;
  }

  blockquote {
    padding-left: 1rem;
    border-left: 2px solid rgba(#0d0d0d, 0.1);
  }

  hr {
    border: none;
    border-top: 2px solid rgba(#0d0d0d, 0.1);
    margin: 2rem 0;
  }
}
</style>
