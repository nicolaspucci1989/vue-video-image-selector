<script>
export default {
  name: 'FileReader',
  data(){
    return {
      file: null,
      reading: false,
      imgUrl: ''
    }
  },
  methods: {
    async doChange(ev) {
      this.file = ev.target.files
      try {
        this.reading = true
        const result = await this.readFile(this.file[0])
        console.log(result)
      } catch (e) {
        console.log(e)
      } finally {
        this.reading = false
      }
    },
    readFile(file) {
      return new Promise((res, rej) => {
      const reader = new FileReader()
        reader.onload = e => res(e.target.result)
        reader.onerror = e => rej(e)
        reader.readAsDataURL(file)
      })
    }
  }
}
</script>

<template>
<div>
  <div>This is a file selector</div>
  <label>Select a file </label>
  <input @change="doChange" type="file"/>
  <div v-show="reading">
    Reading file...
  </div>
</div>
</template>
