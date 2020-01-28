<template>
  <div class="modal" v-if="show">
    <div class="modal-content">
      <h3>Paste Youtube URL</h3>
      <!-- <label for="url">Paste Youtube URL:</label><br> -->
      <input v-model="url" id="url" />
      <footer class="modal-footer">
        <button @click="insertVideo">Add Video</button>
        <button @click="show = false;">Close modal</button>
      </footer>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: "",
      command: null,
      show: false
    };
  },
  computed: {
    youtubeID() {
      return this.youtubeParser(this.url);
    }
  },
  methods: {
    youtubeParser(url) {
      const regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#?]*).*/;
      const match = url.match(regExp);
      return match && match[7].length === 11 ? match[7] : false;
    },
    showModal(command) {
      // Add the sent command
      this.command = command;
      this.show = true;
    },
    insertVideo() {
      // Some check can be done here, like if `youtubeID` is not false.
      const data = {
        command: this.command,
        data: {
          src: this.youtubeID
        }
      };

      this.$emit("onConfirm", data);
      this.show = false;
    }
  }
};
</script>

<style scoped>
.modal {
  display: flex;
  align-items: center;
  justify-content: center;
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 9999999999
}

.modal-content {
  background-color: #fff;
  padding: 30px;
  border-radius: 8px;
}

.modal-footer {
  margin-top: 20px;
}

button + button {
  margin-left: 10px;
}
#url{
    padding:10px;
    width:400px
}
</style>
