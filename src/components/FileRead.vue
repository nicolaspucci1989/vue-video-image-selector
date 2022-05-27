<script>
export default {
  name: 'FileReader',
  data(){
    return {
      file: null,
      src: '',
    }
  },
  computed: {
    isImage() {
      return this.file.type.startsWith('image')
    },
    isVideo() {
      return this.file.type.startsWith('video')
    }
  },
  methods: {
    async doChange(ev) {
      this.file = ev.target.files[0]
      try {
        const url = window.URL || window.webkitURL
        if (this.isImage) {
          this.src = url.createObjectURL(this.file)
          const imageData = await this.readImageFromUrl(this.src)
          console.log(imageData)
        }
        else if (this.isVideo) {
          this.src = url.createObjectURL(this.file)
          const videoData = await this.getVideoMetadata(this.$refs.video)
          console.log(videoData)
          this.$refs.video.load()
        }
      } catch (e) {
        console.log(e)
      }
    },
    readFile(file) {
      return new Promise((res, rej) => {
      const reader = new FileReader()
        reader.onload = e => res(e.target.result)
        reader.onerror = e => rej(e)
        reader.readAsDataURL(file)
      })
    },
    getVideoMetadata(ref) {
      return new Promise((res) => {
        ref.onloadedmetadata = (e) => res({
          width: e.target.videoWidth,
          height: e.target.videoHeight
        })
      })
    },
    readImageFromUrl(url) {
      return new Promise((res, rej) => {
        const img = new Image()
        img.onload = () => res({
          width: img.width,
          height: img.height
        })
        img.onerror = e => rej(e)
        img.src = url
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
  <div>
    <img v-show="file && isImage" :src="src" alt="some image"/>
    <video ref="video" v-show="file && isVideo" :src="src" type="video/mp4" controls/>
  </div>
</div>
</template>
