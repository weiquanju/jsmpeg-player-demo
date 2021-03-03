<template>
  <div :class="`jsmpeg ${fullStatus ? 'fullRote' : ''}`" :showBar="showBar">
    <!-- <canvas ref="canvas" @click="showBar = !showBar" id="canvas" /> -->
    <div class="jsmpeg-icon _play" v-if="!playStatus" @click="play" />
    <div :class="`jsmpeg-bar ${showBar ? '_show' : '_hide'}`">
      <div
        :class="`jsmpeg-bar-icon ${!playStatus ? '_play' : '_pause'}`"
        @click="play"
      />
      <div
        :class="`jsmpeg-bar-full ${fullStatus ? '_exit' : '_fill'} `"
        @click="fullScreen"
      />
    </div>
  </div>
</template>
<script>
let barTimer = 0;
let fullTimer = 0;

export default {
  props: {
    url: {
      type: String,
    },
  },
  data() {
    return {
      showBar: true,
      player: null,
      playStatus: false,
      fullStatus: false,
    };
  },
  watch: {
    showBar: {
      immediate: true,
      handler() {
        if (barTimer) clearTimeout(barTimer);
        if (this.showBar) setTimeout(() => (this.showBar = false), 3000);
      },
    },
    url: {
      immediate: true,
      handler() {
        this.$nextTick(() => {
          this.initPlayer();
        });
      },
    },
  },
  methods: {
    fullScreen() {
      if (fullTimer) {
        return null;
      }
      console.log(this.fullStatus);
      this.fullStatus
        ? document.exitFullscreen()
        : document.documentElement.requestFullscreen();
      this.fullStatus = !this.fullStatus;
      // 节流
      fullTimer = setTimeout(() => (fullTimer = 0), 500);
    },
    initPlayer() {
      this.destroy();
      let canvas = document.querySelector("#canvas");
      let jsmpeg = document.querySelector(".jsmpeg");
      if (canvas) {
        jsmpeg.removeChild(canvas);
      }
      canvas = document.createElement("canvas");
      canvas.id = "canvas";
      canvas.style.width = "100%";
      canvas.style.height = "100%";
      canvas.addEventListener("touchstart", () => {
        this.showBar = !this.showBar;
      });
      jsmpeg.prepend(canvas);
      console.log(jsmpeg);
      this.player = new JSMpeg.Player(this.url, {
        canvas: canvas,
        onPlay: () => {
          this.playStatus = true;
        },
        onPause: () => {
          this.playStatus = false;
        },
      });
    },
    play() {
      this.playStatus ? this.player.pause() : this.player.play();
    },
    destroy() {
      if (this.player) {
        this.player.destroy();
      }
    },
  },
  destroyed() {
    this.destroy();
  },
};
</script>
<style scoped lang="scss">
canvas,
.jsmpeg {
  height: 100%;
  width: 100%;
  background: #000;
}
.jsmpeg {
  position: relative;
  transform-origin: center;
  &.fullRote {
    position: fixed;
    width: 100vw;
    height: 100vh;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    z-index: 10000;
  }
  &-icon {
    width: 10vw;
    height: 10vw;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);

    background-position: center;
    background-repeat: no-repeat;
    background-size: 80%;
    &._pause {
      background-image: url("/player/pause-circle-line.svg");
    }
    &._play {
      background-image: url("/player/play-circle-line.svg");
    }
  }
  &-bar {
    width: 100%;
    height: 8vw;
    background: rgba(0, 0, 0, 0.3);
    position: absolute;
    bottom: 0;
    &._show {
      display: block;
    }
    &._hide {
      display: none;
    }
    &-full {
      position: absolute;
      right: 0;
      height: inherit;
      width: 8vw;
      background-position: center;
      background-repeat: no-repeat;
      background-size: 80%;
      &._exit {
        background-image: url("/player/fullscreen-exit.svg");
      }
      &._fill {
        background-image: url("/player/fullscreen-fill.svg");
      }
    }
    &-icon {
      position: absolute;
      left: 0;
      height: inherit;
      width: 8vw;
      background-position: center;
      background-repeat: no-repeat;
      background-size: 80%;
      &._pause {
        background-image: url("/player/pause-line.svg");
      }
      &._play {
        background-image: url("/player/play-line.svg");
      }
    }
  }
}
</style>