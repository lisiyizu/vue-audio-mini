<style scoped>
/*css 部分*/
.circleProgress_wrapper{ /*设置圆环的大小*/
  margin: auto;
  position: relative;
  width: 100px;
  height: 100px;
  overflow: hidden;
  border-radius: 50%;
  box-sizing: border-box;
  cursor: pointer;
}

.circleProgress_wrapper:before{
  position: absolute;
  content: "";
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,.2);
  background-size: cover;
  border-radius: 50%;
}

.circleProgress_wrapper.rotate:before{
  animation:audioLogoRotate 10s linear infinite;
}
@keyframes audioLogoRotate{
  0%{
    transform: rotate(0deg)
  }
  100%{
    transform: rotate(360deg);
  }
}
.circleProgress_wrapper:after{
  position: absolute;
  top: 4px;
  left: 4px;
  content: "";
  width: 92px;
  height: 92px;
  border-radius: 50%;
  background-color:rgba(253,253,253,0.3); 
  background-image:url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACYAAAAoCAMAAACl6XjsAAAAaVBMVEX///8AAAD///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////9ThU/0AAAAI3RSTlPMAJDBnhcQxrqzemtSOiMGAoZfLKqkjXBECoiFhGJIRi4MC3M9axoAAACkSURBVDjLrZRLDoMwDERdwKGkTSkt/X/h/ocEsZmdPVJ46ydFGdsj4VpWaeMhN5kpDp5WyEIMlCblqXW1heZnaqA6mxrolNKk3o2WBrb7o6WBPhEa4nY1qWNwNMRtaaC5exrihmbxVltD3A9HQ9zQLC5KadKRGv0o/4X8QF5KDosfff4ixbDWkvcp9wAR50Cc81OZqvkzxfVlavDTkqWaX9Fk4U9wrwfK2T44TgAAAABJRU5ErkJggg==');
  background-repeat:no-repeat;
  background-position:center;
  background-size:18px 22px;
}
.circleProgress_wrapper.rotate:after{
  background-image:url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAiCAMAAAAJbCvNAAAAM1BMVEX///8AAAD///////////////////////////////////////////////////////////+Q8lQ3AAAAEXRSTlPmAOEXHcLcvrtvZlER1IyHDEwQIaQAAABJSURBVDjL7ctbEoAgDEPRBLWK4mP/q/XDIGUJzPT+NXOKY1u+1p2qm2D4ewTONhXCNQmkNs0BAgQYFrirCJj/Qa4i3VTZ6nSRL9xqAzIch8kgAAAAAElFTkSuQmCC');
  background-size:18px 22px;
}
/*这是一个街区了左半部分，只显示右半部分的div*/
.audiomini{
  position: absolute;/*必须是绝对定位元素，clip属性才会有效*/
  width: 100%;
  height: 100%;
  clip: rect(0,100px,100px,50px);
}
.audiomini.gt50{/*当进度超过50%时，取消裁剪*/
  clip: rect(auto, auto, auto, auto);
}
.bar,.fill{/*两个只显示左半部分的半圆*/
  position: absolute;
  box-sizing: border-box;
  width: 100%;
  height: 100%;
  border: 4px rgba(255, 255, 253, 1) solid;
  border-radius: 50%;
  clip: rect(0, 50px, 100px, 0);
}
.audiomini.gt50 .fill{/*当进度超过50%时，让fill旋转180度填充50%*/
  transform: rotate(180deg);
}
.loadingDiv{
    width: 100%;
    height: 100%;
    position: absolute;
    z-index:1;
   background-color:rgba(255, 255, 253, .6); 
}
.loadingAnimate {
    width: 100%;
    height: 100%;
    position: absolute;
    background-repeat:no-repeat;
    background-position:center;
    z-index:2;
    background-image:url("../assets/loading.gif")
  }
</style>
<template>
<div class="circleProgress_wrapper" :class='model.elclass' @click='toggleAudio'>
  <div v-show='loading' class='loadingDiv'></div>
  <div v-show='loading' class="loadingAnimate"></div>
  <div :id="model.elid" class="audiomini">
    <div class="bar pre50"></div>
    <div class="fill"></div>
  </div>
  
</div>  
</template>
<script>
    export default {
      data(){
        return {
            loading:false
        }
      },
      name:"vue-audio-mini",
      props:{
            model:{
              type:Object,
              twoWay:true
            },
            callback:{
              type:Function
            }
      },
      methods:{
            addClass  (element, className){
        let oldClass = element.className;
        oldClass.indexOf(className)<0&&(element.className=oldClass+" "+className);
      },
      removeClass  (element, className){
        element.className = element.className.replace(new RegExp("\\s"+className),"");
      },
      toggleAudio (){
                // 初始化音频组件
        if(this.model.state==-1){
           this.init();
        }

        if(typeof this.callback == 'function') this.callback.call(this.model,this.model);

        let audioEl = document.querySelector("#audio-mini-controll");

        if(audioEl.src!=this.model.src && audioEl.src){
          audioEl.pause();
          audioEl.currentTime = 0;
        }

        console.log(audioEl.paused);

                if(audioEl.paused){
                  if(audioEl.currentTime==0){
                    audioEl.src = this.model.src;
                  }
          audioEl.play();
          this.model.state = 1;
        }else{
          audioEl.pause();
          this.model.state = 0;
        }
      },
      init(){
        let vm = this;

          let bar = document.querySelector("."+this.model.elclass+" .bar"),
            fill = document.querySelector("."+this.model.elclass+" .fill"),
          process = 0,
          circleProgress = document.querySelector("."+this.model.elclass),
          audiomini = document.querySelector("#"+this.model.elid);

            //  圆弧音频播放控件
            let audioController  = function (opt) {
              let audioEl = document.querySelector("#audio-mini-controll");

              if(audioEl) {
                document.querySelector("body").removeChild(document.querySelector("#audio-mini-controll"));
              }

                    audioEl = document.createElement('audio');  //生成一个audio元素
                    audioEl.id = 'audio-mini-controll';
          audioEl.controls = true;  //这样控件才能显示出来
          audioEl.src = '';  //音乐的路径
          document.body.appendChild(audioEl);  //把它添加到页面中
                audioEl.style.display = 'none';
                    
                    // 移除事件
                    for(let  key in opt.events){
                audioEl.removeEventListener(key, opt.events[key], false);
            }
                    
                    // 动态绑定事件
              for(let  key in opt.events){
                audioEl.addEventListener(key,  opt.events[key]);
              }

              return audioEl; 
            };
              
              // 创建音频控件
        let audioObj = new audioController({
          src: vm.model.src,
          events: {
            "play": function(){
              vm.addClass(circleProgress, "rotate");
            },
            "waiting":function(){
                console.log("正在加载进度。。waiting");
                vm.loading = true;
            },
            "timeupdate": function() {

              console.log(audioObj.currentTime);

              console.log(audioObj.readyState);

              if(audioObj.readyState==4){
                vm.loading = false;
              }else if(audioObj.readyState==2) {
                vm.loading = true;
              }

              if(vm.model.state==-1) {
                  bar.style="";
                  vm.loading = false;
                  return;
              }

              //监听timeupdate事件，也就是音频当前播放进度发生改变响应的事件
              /**
               *  它们的比正好就是当前播放进度
               *   再乘以360转化为进度条应该旋转的角度
               */
              process = (audioObj.currentTime / audioObj.duration) * 360;
                parseInt(process) === 180 && vm.addClass(audiomini, "gt50");
                bar.style.transform="rotate("+(process)+"deg)";
            },
            "pause":function(){
              vm.removeClass(circleProgress,"rotate");
            },
            "ended":function(){
              vm.removeClass(circleProgress,"rotate");
              vm.removeClass(audiomini,"gt50");
              bar.style.transform="rotate(0deg)";
            }
          }
        });
      }
      }
    }
</script>