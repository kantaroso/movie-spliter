<template>
  <b-container fluid>
    <h2>動画切り取り</h2>
    <b-form-file
      v-model="file1"
      :state="Boolean(file1)"
      placeholder="Choose a file or drop it here..."
      drop-placeholder="Drop file here..."
      @input="file_input"
    ></b-form-file>
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
    <b-button class="my-1" variant="primary" @click="execute">変換</b-button>
    <div class="my-1" v-if="blob_url">
      <b-embed
        type="video"
        aspect="16by9"
        :src="blob_url"
        allowfullscreen
        controls
      >
      </b-embed>
    </div>
    <b-modal
      id="modal-progress"
      :no-close-on-backdrop="true"
      :no-close-on-esc="true"
      :hide-footer="true"
      :hide-header="true"
      :centered="true"
    >
      <div class="text-center">変換中... <b-spinner label="Loading..."></b-spinner></div>
    </b-modal>
    <b-modal
      id="modal-compleated"
      title="変換完了"
      :hide-header-close="true"
      :ok-only="true"
    >
      ファイル名<br>{{process_params.output}}
    </b-modal>
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
      file1: null,
      blob_url: '',
      process_params: {}
    }
  },
  methods: {
    execute () {
      this.$bvModal.show('modal-progress')
      this.process_params = {
        start_seccond: this.start_seccond,
        total_seccond: this.total_seccond,
        input: this.file1.path,
        output: this.file1.path.replace(this.file1.name, `extract_${this.start_seccond}_${parseInt(this.start_seccond) + parseInt(this.total_seccond)}_` + this.file1.name)
      }
      ipcRenderer.on('movie-split-compleated', () => {
        this.$bvModal.hide('modal-progress')
        this.$bvModal.show('modal-compleated')
      })
      ipcRenderer.on('movie-split-error', (msg) => {
        this.$bvModal.hide('modal-progress')
        alert(msg)
      })
      ipcRenderer.send('movie-split', this.process_params)
    },
    file_input (file) {
      this.blob_url = URL.createObjectURL(file)
    }
  }
}
</script>
