<template>
  <button @click="chooseFile">Choisir un fichier</button>
</template>

<script>
export default {
  methods: {
    async uploadFile (file) {
      const reader = new FileReader()
      reader.addEventListener('load', event => {
        this.$emit('done', event.target.result)
      })
      reader.readAsDataURL(file)
    },
    chooseFile () {
      const input = document.createElement('input')
      input.type = 'file'
      input.name = 'file'
      input.accept = '.jpg,.jpeg,.png'
      input.style.position = 'fixed'
      input.style.top = '-1000px'
      document.body.appendChild(input)
      input.addEventListener('change', _ => {
        this.uploadFile(input.files[0])
        document.body.removeChild(input)
      })
      input.click()
    }
  }
}
</script>
