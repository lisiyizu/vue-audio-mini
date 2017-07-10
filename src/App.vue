<template>
  <div id="app">
    <div v-for='item in audioList' style='margin:10px;'>
       <vue-audio-mini :model.sync='item' :callback='clickAudio'></vue-audio-mini>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data(){
    return {
      audioList:[{
        elid:"audio-ctr-1",
        elclass:"audio-ctr-1",
        state:-1,
        src:"http://lisiyizu.github.io/%E4%B9%8C%E5%85%8B%E4%B8%BD%E4%B8%BD.mp3"
      },{
        
        elid:"audio-ctr-2",
        elclass:"audio-ctr-2",
        state:-1,
        src:"http://lisiyizu.github.io/%E4%B8%8B%E4%B8%80%E7%AB%99%E5%A4%A9%E5%90%8E.mp3"
      }]
    }
  },
  methods:{
    clickAudio(m){
      this.audioList.forEach(item=>{
          if(m!=item) {
            // 重置音频的进度
            item.state=-1;
            document.querySelector("."+item.elclass+" .bar").style.transform='rotate(0deg)';
          }
      })
    }
  },
  components:{
     'vue-audio-mini':require("./components/vue-audio-mini.vue")
  }
}
</script>

<style>
body{
    background: cornflowerblue;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
