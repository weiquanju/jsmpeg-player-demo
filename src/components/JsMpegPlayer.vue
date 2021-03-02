<template>
  <div :class="`jsmpeg ${fullStatus ? 'fullRote' : ''}`">
    <!-- <canvas id="player-canvas" @touchstart="showBar = !showBar" /> -->
    <div class="jsmpeg-icon _play" v-if="!playStatus" @touchstart="play" />
    <div :class="`jsmpeg-bar ${showBar ? '_show' : '_hide'}`">
      <div :class="`jsmpeg-bar-icon ${!playStatus ? '_play' : '_pause'}`" @touchstart="play" />
      <div :class="`jsmpeg-bar-full ${fullStatus ? '_exit' : '_fill'} `" @touchstart="fullScreen" />
    </div>
  </div>
</template>
<script>
let barTimer = 0;
let fullTimer = 0;

export default {
  props: {
    url: {
      type: String
    }
  },
  data() {
    return {
      showBar: true,
      render: false,
      player: null,
      playStatus: false,
      fullStatus: false
    };
  },
  watch: {
    showBar: {
      immediate: true,
      handler() {
        if (barTimer) clearTimeout(barTimer);
        if (this.showBar) setTimeout(() => (this.showBar = false), 3000);
      }
    },
    url: {
      immediate: true,
      handler() {
        this.destroy();
        this.$nextTick(() => {
          this.$nextTick(() => this.initPlayer());
        });
      }
    }
  },
  methods: {
    noop(...args) {
      console.log(...args);
    },
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
      fullTimer = setTimeout(() => (fullTimer = 0), 1000);
    },
    initPlayer() {
      // showBar = !showBar
      let canvas = document.querySelector("#player-canvas");
      let icon = document.querySelector(".jsmpeg-icon");
      let jsmpeg = document.querySelector(".jsmpeg");
      if (!canvas) {
        canvas = document.createElement("canvas");
        canvas.id = "player";
        canvas.style.width = "100%";
        canvas.style.height = "100%";
        canvas.addEventListener("touchstart", () => {
          this.showBar = !this.showBar;
        });
        jsmpeg.insertBefore(canvas, icon);
      }
      this.$nextTick(() => {
        this.player = new JSMpeg.Player(this.url, {
          canvas: canvas,
          onPlay: player => {
            // console.log("player playing");
            this.playStatus = true;
          },
          onPause: player => {
            // console.log("player pause");
            this.playStatus = false;
          }
        });
      });
    },
    play() {
      // console.log("player status", this.playStatus);
      this.playStatus ? this.player.pause() : this.player.play();
    },
    destroy() {
      let canvas = document.getElementById("#player-canvas");
      if (canvas) {
        let jsmpeg = document.querySelector(".jsmpeg");
        console.log("remove canvas", jsmpeg, canvas);
        jsmpeg.removeChild(canvas);
      }
      if (this.player) {
        this.playStatus && this.player.stop();
        this.player.destroy();
      }
    }
  },
  destroyed() {
    this.destroy();
  }
};
</script>
<style scoped lang="scss">
canvas,
.jsmpeg {
  height: 100%;
  width: 100%;
}
.jsmpeg {
  position: relative;
  transform-origin: center;
  &.fullRote {
    position: fixed;
    width: 100vh;
    height: 100vw;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%) rotate(90deg);
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
      background-image: url("/assets/images/svg/player/pause-circle-line.svg");
    }
    &._play {
      background-image: url("/assets/images/svg/player/play-circle-line.svg");
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
        background-image: url("/assets/images/svg/player/fullscreen-exit.svg");
      }
      &._fill {
        background-image: url("/assets/images/svg/player/fullscreen-fill.svg");
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
        background-image: url("/assets/images/svg/player/pause-line.svg");
      }
      &._play {
        background-image: url("/assets/images/svg/player/play-line.svg");
      }
    }
  }
}
</style>