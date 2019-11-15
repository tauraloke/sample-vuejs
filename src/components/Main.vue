<template>
  <div class="container">
  
    <div v-if="camera_is_visible" class="camera-block">
      <video ref="video" id="video" v-bind:width="video_width" v-bind:height="video_height" autoplay></video>
      <canvas ref="canvas" id="canvas"  v-bind:width="video_width"  v-bind:height="video_height"></canvas>
      <img class="face_img" src="../assets/face.svg" />
      <div class="videostrea-title">Video stream</div>
    </div>
    <div v-else>
      <img class="photos_img" src="../assets/photos.svg" title="Click button below to start!" />
    </div>

    <div class="control-buttons">
      <div class="opencamera" v-if="!camera_is_visible" v-on:click="open_camera()">
        <div>Open camera</div>
      </div>
      <div class="capture" v-if="camera_is_visible" id="snap" v-on:click="capture()">
        <div>Capture</div>
      </div>
      <div class="clearhisto" v-if="captures && captures.length > 0" v-on:click="clear_history()">
        <div>Clear history</div>
      </div>
      <div class="back" v-if="camera_is_visible" v-on:click="remove_camera()">
        <div>Back</div>
      </div>
    </div>

    <div class="history" v-if="captures && captures.length > 0">
      <div class="history-header">History</div>
      <ul class="history-list">
        <li v-for="c in captures">
          <img v-bind:src="c"  v-bind:height="history_height"  v-bind:width="history_width" />
          <div class="history-element-title">Photo</div>
        </li>
      </ul>   
    </div>
	
  </div>
</template>

<script>

  export default {
    name: 'Main',
    data() {
      return {
        video: {},
        camera_is_visible: false,
        canvas: {},
        captures: [],
        video_width: 640,
        video_height: 360,
        history_width: 320,
        history_height: 180,
      }
    },
    created() {
      if(this.is_mobile()) {
        this.video_width = 320;
        this.video_height = 320;
        this.history_width = 100;
        this.history_height = 100;
      }
    },
    mounted() {
    },
    methods: {
      is_mobile() {
        var check = false;
        (function(a){if(/(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows ce|xda|xiino/i.test(a)||/1207|6310|6590|3gso|4thp|50[1-6]i|770s|802s|a wa|abac|ac(er|oo|s\-)|ai(ko|rn)|al(av|ca|co)|amoi|an(ex|ny|yw)|aptu|ar(ch|go)|as(te|us)|attw|au(di|\-m|r |s )|avan|be(ck|ll|nq)|bi(lb|rd)|bl(ac|az)|br(e|v)w|bumb|bw\-(n|u)|c55\/|capi|ccwa|cdm\-|cell|chtm|cldc|cmd\-|co(mp|nd)|craw|da(it|ll|ng)|dbte|dc\-s|devi|dica|dmob|do(c|p)o|ds(12|\-d)|el(49|ai)|em(l2|ul)|er(ic|k0)|esl8|ez([4-7]0|os|wa|ze)|fetc|fly(\-|_)|g1 u|g560|gene|gf\-5|g\-mo|go(\.w|od)|gr(ad|un)|haie|hcit|hd\-(m|p|t)|hei\-|hi(pt|ta)|hp( i|ip)|hs\-c|ht(c(\-| |_|a|g|p|s|t)|tp)|hu(aw|tc)|i\-(20|go|ma)|i230|iac( |\-|\/)|ibro|idea|ig01|ikom|im1k|inno|ipaq|iris|ja(t|v)a|jbro|jemu|jigs|kddi|keji|kgt( |\/)|klon|kpt |kwc\-|kyo(c|k)|le(no|xi)|lg( g|\/(k|l|u)|50|54|\-[a-w])|libw|lynx|m1\-w|m3ga|m50\/|ma(te|ui|xo)|mc(01|21|ca)|m\-cr|me(rc|ri)|mi(o8|oa|ts)|mmef|mo(01|02|bi|de|do|t(\-| |o|v)|zz)|mt(50|p1|v )|mwbp|mywa|n10[0-2]|n20[2-3]|n30(0|2)|n50(0|2|5)|n7(0(0|1)|10)|ne((c|m)\-|on|tf|wf|wg|wt)|nok(6|i)|nzph|o2im|op(ti|wv)|oran|owg1|p800|pan(a|d|t)|pdxg|pg(13|\-([1-8]|c))|phil|pire|pl(ay|uc)|pn\-2|po(ck|rt|se)|prox|psio|pt\-g|qa\-a|qc(07|12|21|32|60|\-[2-7]|i\-)|qtek|r380|r600|raks|rim9|ro(ve|zo)|s55\/|sa(ge|ma|mm|ms|ny|va)|sc(01|h\-|oo|p\-)|sdk\/|se(c(\-|0|1)|47|mc|nd|ri)|sgh\-|shar|sie(\-|m)|sk\-0|sl(45|id)|sm(al|ar|b3|it|t5)|so(ft|ny)|sp(01|h\-|v\-|v )|sy(01|mb)|t2(18|50)|t6(00|10|18)|ta(gt|lk)|tcl\-|tdg\-|tel(i|m)|tim\-|t\-mo|to(pl|sh)|ts(70|m\-|m3|m5)|tx\-9|up(\.b|g1|si)|utst|v400|v750|veri|vi(rg|te)|vk(40|5[0-3]|\-v)|vm40|voda|vulc|vx(52|53|60|61|70|80|81|83|85|98)|w3c(\-| )|webc|whit|wi(g |nc|nw)|wmlb|wonu|x700|yas\-|your|zeto|zte\-/i.test(a.substr(0,4))) check = true;})(navigator.userAgent||navigator.vendor||window.opera);
        return check;
      },
      open_camera() {
        if(navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
          navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
            this.camera_is_visible = true;
            this.$nextTick(() => {
              this.video = this.$refs.video;
              this.video.srcObject = stream;
              this.video.play();              
            });
          });
        }
      },
      remove_camera() {
        let stream = this.video.srcObject;
        let tracks = stream.getTracks();
        tracks.forEach(function(track) {
          track.stop();
        });
        this.video.srcObject = null;
        this.camera_is_visible = false;
      },
      capture() {
        this.canvas = this.$refs.canvas;
        var context = this.canvas.getContext("2d").drawImage(this.video, 0, 0, 640, 480);
        this.captures.push(canvas.toDataURL("image/png"));
      },
      clear_history() {
        this.captures = []
      }
    }
  }

</script>




<style scoped>

  @font-face {
    font-family: "LucidaGrande-Bold";
    src: url("../assets/LucidaGrande-Bold.ttf") format("opentype");
  }


  .container {
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
  
  

  #video {
    background-color: #000000;
  }

  #canvas {
    display: none;
    border: solid 1px #979797;
    background: #d8d8d8;
  }

  .camera-block {
    position: relative;
    width: 700px;
    margin: auto;
  }
  
  .face_img {
    position: absolute;
    top: 35px;
    left: 273px;
  }
  
  .videostrea-title {
    color: #898989;
    font-family: LucidaGrande-Bold;
    font-size: 18px;
    font-weight: 400;
    position: absolute;
    right: 59px;
    bottom: 19px;
  }
  
  
  
  .control-buttons {
    margin-top: 50px;
  }
  
  .control-buttons > div {
    box-shadow: 5px 5px 30px rgba(0,0,0,0.25);
    cursor: pointer;
    user-select: none;
    width: 150px;
    height: 50px;
    border-radius: 5px;
    display: inline-block;
    margin-right: 29px;
    margin-left: 29px;
  }
  
  
  .opencamera {
    background: #4b9c7b;
  }
  
  .clearhisto {
    background: #c85b5b;
  }
  
  .back {
    background: #ffffff;
  }
  
  .capture {
    background: #4b9c7b;
  }
  
  .control-buttons > div > div {
    color: #ffffff;
    font-family: "LucidaGrande-Bold";
    font-size: 16px;
    font-weight: 400;
    padding-top: 15px;
  }
  
  .back > div {
    color: #4b9c7b !important;
  }
  
  

  .history {
    width: 1200px;
    text-align: left;
    margin-top: 100px;
    margin-left: auto;
    margin-right: auto;
  }
  
  .history-header {
    color: #283f5c;
    font-family: "LucidaGrande-Bold";
    font-size: 28px;
    font-weight: 400;
    padding-left: 10px;
  }
  
  .history-element-title {
    color: #898989;
    font-family: "LucidaGrande-Bold";
    font-size: 18px;
    font-weight: 400;
    display: inline-block;
    position: absolute;
    bottom: 51px;
    right: 30px;
  }
  
  .history-list {
    padding: 0;
  }
  
  .history-list > li {
    display: inline;
    padding: 5px;
    position: relative;
    margin-right: 24px;
  }

  .history-list > li > img {
    border: solid 1px #979797;
    margin-bottom: 33px;
  }
    

  
  /* mobile media */
  @media only screen and (max-width: 700px) {
    .container {
      margin-top: 5px;
    }
  
    .camera-block {
      width: auto;
    }
    
    .face_img {
      left: 99px;
    }
  
    .control-buttons {
      margin-top: 20px;
    }
    .control-buttons > div {
      margin-top: 20px;
    }
    
    .history {
      margin-top: 10px;
      width: auto;
    }
    
    .history-header {
      text-align: center;
    }
    
    .history-list > li {
      margin-right: 5px;
      padding: 0px;
    }
    
    .history-list > li > img {
      margin-bottom: 5px;
    }
    
    .history-element-title {
      bottom: 16px;
      right: 7px;
    }
  }


</style>

























