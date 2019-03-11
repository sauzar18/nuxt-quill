<template>
  <div class="container">
    <h1>Nuxt.jsにQuillエディターいれてみた</h1>
    <no-ssr placeholder="Loading Your Editor...">
      <vue-editor
        id="editor"
        v-model="content"
        @imageAdded="handleImageAdded"
      />
    </no-ssr>
    <div v-html="content" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      content: ''
    }
  },
  methods: {
    handleImageAdded(file, Editor, cursorLocation, resetUploader) {
      const formData = new FormData()
      formData.append('image', file)
      const config = {
        headers: {
          'content-type': 'multipart/form-data',
          'Authorization': 'Bearer ',
          'X-CSRF-TOKEN': ''
        }
      }
      this.$axios.$post('/api_url', formData, config)
        .then((result) => {
          const url = result.data.url // Get url from response
          Editor.insertEmbed(cursorLocation, 'image', url)
          resetUploader()
        })
        .catch((err) => {
          // eslint-disable-next-line no-console
          console.log(err)
        })
    }
  }
}
</script>

<style scoped>
.container {
  width: 980px;
  margin: 60px auto;
}
h1 {
  font-size: 18px;
  margin-bottom: 20px;
  font-weight: normal;
}
</style>
