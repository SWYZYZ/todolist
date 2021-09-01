<template>
  <div id="app">
    <div class="titleBar">
      <div class="title">
        <div class="logo">
          <img src="@/assets/logo.png" alt="" />
        </div>
        <div class="txt">todolist</div>
      </div>
      <div class="tools">
        <div title="作者：王淳灏&#10;QQ:1617220899&#10;微信:13473291578">
          <i class="iconfont icon-wenti"></i>
        </div>
        <div class="pin pinnone" v-if="isTop" @click="pinnone" title="取消置顶">
          <i class="iconfont icon-pin-fill"></i>
        </div>
        <div class="pin" v-else @click="pintop" title="置顶">
          <i class="iconfont icon-pin"></i>
        </div>
        <div @click="reload" title="刷新">
          <i class="iconfont icon-shuaxin"></i>
        </div>
        <div @click="minsize" title="最小化">
          <i class="iconfont icon-sami-select"></i>
        </div>
        <div v-if="isMaxSize" @click="restore" title="还原">
          <i class="iconfont icon-suoxiao"></i>
        </div>
        <div v-else @click="maxsize" title="最大化">
          <i class="iconfont icon-fangda"></i>
        </div>
        <div @click="close" class="close" title="关闭">
          <i class="iconfont icon-shanchu"></i>
        </div>
      </div>
    </div>
    <div class="body">
      <router-view />
    </div>
  </div>
</template>

<script>
let { remote } = window.require("electron");
window.onkeydown = function (e) {
  if (e.keyCode == 82 && (e.ctrlKey || e.metaKey)) {
    return false;
  }
  // if(e.keyCode == 73 && (event.shiftKey || e.metaKey) && (e.ctrlKey || e.metaKey)){
  //   return false;
  // }
};
let self;
export default {
  data() {
    return {
      isMaxSize: false,
      isTop:false,
    };
  },
  methods: {
    reload(){
      location.reload();
    },
    close() {
      remote.getCurrentWindow().close();
    },
    minsize() {
      remote.getCurrentWindow().minimize();
    },
    restore() {
      remote.getCurrentWindow().restore();
    },
    maxsize() {
      remote.getCurrentWindow().maximize();
    },
    debounce(fn) {
      let timeout = null;
      return function () {
        clearTimeout(timeout);
        timeout = setTimeout(function () {
          fn.apply(this, arguments);
        }, 300);
      };
    },
    setState() {
      let win = remote.getCurrentWindow();
      let rect = win.getBounds();
      let isMaxSize = win.isMaximized();
      let obj = { rect, isMaxSize };
      localStorage.setItem("winState", JSON.stringify(obj));
    },
    getState() {
      let win = remote.getCurrentWindow();
      let winState = localStorage.getItem("winState");
      if (winState) {
        winState = JSON.parse(winState);
        if (winState.isMaxSize) {
          win.maximize();
        } else {
          win.setBounds(winState.rect);
        }
      }
      win.show();
    },
    pintop(){
      self.isTop = true
      remote.getCurrentWindow().setAlwaysOnTop(true);
    },
    pinnone(){
      self.isTop = false
      remote.getCurrentWindow().setAlwaysOnTop(false);
    }
  },
  mounted() {
    let win = remote.getCurrentWindow();
    win.on("maximize", (_) => {
      self.isMaxSize = true;
      self.setState();
      console.log(_);
    });
    win.on("unmaximize", (_) => {
      self.isMaxSize = false;
      self.setState();
      console.log(_);
    });
    win.on(
      "move",
      self.debounce(() => {
        self.setState();
      })
    );
    win.on(
      "resize",
      self.debounce(() => {
        self.setState();
      })
    );
    self.isMaxSize = win.isMaximized();
    self.getState();
  },
  created() {
    self = this;
  },
};
</script>
<style>
@import url("./css/common_new.css");
@import url("./font/f1//iconfont.css");
@import url("./font/f2/iconfont.css");
@import url("./css/style.css");
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}

</style>
