<template>
  <b-container fluid>
    <h2>動画切り取り</h2>
    <b-form-file
      v-model="file1"
      :state="Boolean(file1)"
      placeholder="Choose a file or drop it here..."
      drop-placeholder="Drop file here..."
    ></b-form-file>
    <div class="mt-3">Selected file: {{ file1 ? file1.name : '' }}</div>
    <b-row class="my-1">
      <b-col sm="3">
        <label for="id-split-seccond">開始時間（秒）:</label>
      </b-col>
      <b-col sm="9">
        <b-form-input v-model="start_seccond" id="id-split-seccond"></b-form-input>
      </b-col>
    </b-row>
    <b-row class="my-1">
      <b-col sm="3">
        <label for="id-split-seccond">再生時間（秒）:</label>
      </b-col>
      <b-col sm="9">
        <b-form-input v-model="total_seccond" id="id-split-seccond"></b-form-input>
      </b-col>
    </b-row>
    <b-button variant="primary" @click="execute">変換</b-button>
  </b-container>
</template>

<script>
const { ipcRenderer } = require('electron')
export default {
  name: 'index',
  data () {
    return {
      start_seccond: 0,
      total_seccond: 30,
      file1: null
    }
  },
  methods: {
    execute () {
      const date = new Date()
      const params = {
        start_seccond: this.start_seccond,
        total_seccond: this.total_seccond,
        input: this.file1.path,
        output: this.file1.path.replace(this.file1.name, date.getTime() + '_output.mp4')
      }
      ipcRenderer.on('movie-split-compleated', (event, arg) => {
        console.log(arg)
      })
      ipcRenderer.send('movie-split', params)
    }
  }
}
</script>
