<template>
  <div>
    <JsMpegPlayerVue class="player" :url="url" />
  </div>
</template>
<script>
import JsMpegPlayerVue from "@/components/JsMpegPlayer.vue";

export default {
  components: {
    JsMpegPlayerVue
  },
  data() {
    return { url: "ws://125.70.208.96:8028" };
  },
  mounted(){
    setTimeout(()=>{
      this.url = 'ws://125.70.208.96:8026'
      console.log('change')
    },3000)
  }
};
</script>
<style scoped lang="scss">
.player {
  width: 60vw;
  height: 9 / 16 * 60vw;
}
#input {
  width: calc(60vw - 2px);
  border: solid 1px #666;
  margin: 1em 0;
  padding: 0;
}
</style>