<template>
  <div>
    <JsMpegPlayerVue class="player" :url="url" />
    <input v-model="url" id="input" />
  </div>
</template>
<script>
import JsMpegPlayerVue from "@/components/JsMpegPlayer.vue";

export default {
  components: {
    JsMpegPlayerVue
  },
  data() {
    return { url: "ws://218.89.244.122:8021" };
  }
};
</script>
<style scoped lang="scss">
.player {
  width: 100vw;
  height: 9 / 16 * 100vw;
}
#input {
  width: calc(100vw - 2px);
  border: solid 1px #666;
  margin: 1em 0;
  padding: 0;
}
</style>