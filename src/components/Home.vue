<template>
  <div>
    <input type="file" ref="fileLoad" accept="image/*" style="display:none;" @change="handleFileLoad">

    <div class="overlayed info-popup" v-show="!image || needHelp">
      <h2 v-show="!image">Nessuna Immagine selezionata!</h2>
      <h2>Lista comandi!</h2>

      <div style="align-self: center;">
        <h4 style="display:flex; justify-content:space-between;">
          <span class="button">u</span> upload dell'immagine</h4>
        <h4 style="display:flex; justify-content:space-between;"><span class="button">frecce</span> sposta il quadrato indicativo</h4>
        <h4 style="display:flex; justify-content:space-between;"><span class="button">spazio</span> scopri la porzione di immagine</h4>
        <h4 style="display:flex; justify-content:space-between;"><span class="button">click</span> sposta il quadro indicativo <br />nella posizione desiderata</h4>
        <h4 style="display:flex; justify-content:space-between;"><span class="button">h</span> mostra/nascondi questo popup</h4>
      </div>

    </div>

    <svg ref="svg" xmlns="http://www.w3.org/2000/svg" x="0" y="0" :width="maxWidth" :height="maxHeight" :viewBox="viewBox" class="overlayed">
        <defs>
        <mask id="theMask">
          <rect v-for="(rect, index) in rectList" id="masker" fill="#fff" width="100" height="100" :key="index" :x="rect.x" :y="rect.y" />
        </mask>
      </defs>
      <g id="maskReveal" mask="url(#theMask)">
        <image :xlink:href="image" x="0" y="0" :width="maxWidth" :height="maxHeight" @click="handleMouseClick" />
      </g>
      <rect fill="#000" width="100" height="100" :x="cx" :y="cy" />
    </svg>

  </div>
</template>

<script lang="ts">
import Vue from "vue";



export default Vue.extend({
  name: "home",
  created() {
    window.addEventListener("keydown", this.handleKeyDown);
    window.addEventListener("keyup", this.handleKeyUp);
  },
  data() {
    return {
      cx: 0,
      cy: 0,
      rectList: [],
      image: "",
      width: 0,
      height: 0,
      innerWidth: window.innerWidth / 1.02,
      innerHeight: window.innerHeight / 1.02,
      needHelp: false
    };
  },
  computed: {
    viewBox() {
      return `0 0 ${this.maxWidth} ${this.maxHeight}`;
    },
    maxWidth() {
      return this.width < this.innerWidth ? this.width : this.innerWidth;
    },
    maxHeight() {
      return this.height < this.innerHeight ? this.height : this.innerHeight;
    }
  },
  methods: {
    handleMouseMovement(e) {
      this.cx = e.offsetX;
      this.cy = e.offsetY;
    },
    handleMouseClick(e) {
      this.cx = e.offsetX - 50;
      this.cy = e.offsetY - 50;
      /* var rect = { x: this.cx, y: this.cy };
      this.rectList.push(rect);*/
    },
    handleFileLoad(e) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length) return;
      this.createImage(files[0]);
    },
    createImage(file) {
      var image = new Image();
      var self = this;

      image.addEventListener("load", function() {
        self.width = this.naturalWidth;
        self.height = this.naturalHeight;
        self.image = this.src;

        self.rectList = [];
      });

      image.src = window.URL.createObjectURL(file);
    },
    handleKeyDown(e) {
      switch (e.code) {
        case "ArrowDown":
          if (this.cy < this.maxHeight) this.cy += 50;
          break;
        case "ArrowUp":
          if (this.cy > 0) this.cy -= 50;
          break;
        case "ArrowLeft":
          if (this.cx > 0) this.cx -= 50;
          break;
        case "ArrowRight":
          if (this.cx < this.maxWidth) this.cx += 50;
          break;
      }
    },
    handleKeyUp(e) {
      e.preventDefault();
      switch (e.code) {
        case "Space":
          var rect = { x: this.cx, y: this.cy };
          this.rectList.push(rect);
          break;
        case "KeyU":
          if (this.$refs.fileLoad) this.$refs.fileLoad.click();
          break;
        case "KeyH":
          this.needHelp = !this.needHelp;
          break;
      }
    }
  }
});
</script>

<style>
.info-popup {
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 50vh;
  width: 80vw;

  z-index: 999;
}

.button {
  background-color: white;
  padding: 3px 5px;
  box-shadow: 1px 1px 5px black;
  border-radius: 3px;
  margin-right: 7px;
}

.overlayed {
  box-shadow: 3px 2px 11px 0px #464d53f2;

  background-color: #c8ccd0f3;

  position: fixed;
  left: 0;
  top: 0;
  bottom: 0;
  right: 0;

  margin: auto;
}
</style>
