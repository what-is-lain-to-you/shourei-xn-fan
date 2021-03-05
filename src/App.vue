<template>
  <v-app id="app">
    <v-dialog v-model="addtoscreendialog" persistent max-width=600>
      <v-card class="pa-1">
        <v-card-title>{{$t("ui.addtohomescreen")}}</v-card-title>
        <v-card-text>{{$t("ui.addtohomescreeninfo")}}</v-card-text>
        <v-row class="ma-0 pa-0" justify="center" align="center">
        <v-img src="img/icons/android-chrome-192x192.png" max-width="100">
        <template v-slot:placeholder>
                    <v-row class="fill-height ma-0" align="center" justify="center">
                      <v-progress-circular indeterminate color="primary"></v-progress-circular>
                    </v-row>
        </template>
        </v-img>
        </v-row>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="secondary" text @click="addtoscreendialog=false">{{$t("ui.dontneed")}}</v-btn>
          <v-btn color="secondary" text @click="neverShowDialog">{{$t("ui.never")}}</v-btn>
          <v-btn color="primary" raised @click="addToHomescreen">{{$t("ui.add")}}</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <Topbar></Topbar>
    <v-content app>
      <v-container fluid class="mt-10">
      <v-fab-transition>
        <v-btn
          class=" "
          v-show="gotop"
          color="primary"
          fab
          dark
          fixed
          bottom
          right
          @click="toTop"
        >
          <v-icon>mdi-arrow-up</v-icon>
          
        </v-btn>
      
      </v-fab-transition>
      <router-view></router-view>
      </v-container>
      </v-content>
        <v-footer style="postition:absolute;bottom:0;" class="py-4" dark height="auto">
          <v-container mx-auto>
            <v-layout wrap>
              <v-flex xs10>
                <v-container>
                  <v-row align="end">
                    特别感谢SASA-button给予的灵感
                    <v-btn icon @click="See('https://github.com/vbup-osc/sasa-button')"><v-icon>mdi-github</v-icon></v-btn>
                  </v-row>
                  <v-row>
                    {{$t("ui.developerinfo")}}
                  </v-row>
                  <v-row align="end" class="pt-2">
                    <span class="vercel-text">Powered by</span>
                    <a href="https://www.vercel.com/?utm_source=vbuposc&utm_campaign=oss" target="_blank" rel="noreferrer">
                      <img class="pl-2" src="img/vercel/dark.svg" alt="vercel" height="24px"/>
                    </a>
                  </v-row>
                </v-container>
              </v-flex>
              <v-spacer />
              <base-btn class="mr-0" square title="Go to top" @click="$vuetify.goTo(0)">
                <v-icon>mdi-chevron-up</v-icon>
              </base-btn>
            </v-layout>
          </v-container>
        </v-footer>
      
    
    
  </v-app>
</template>

<script>
import Topbar from "./components/Topbar.vue"
export default {
  name: 'App',

  components: {
    Topbar,
  },

  data: () => ({
    gotop:false,
    prompt:false,
    deferred:null,
    addtoscreendialog:false,
    //
  }),
  mounted(){
    let timeNow = new Date();
    let hours = timeNow.getHours();
    if (hours<6||hours>18){//自动触发夜间模式
      this.$store.commit("change_dark_mode");
      this.$vuetify.theme.dark = this.$store.state.dark_mode;
      window.console.log("success")
    }
    window.onbeforeinstallprompt = (e) => {     //当浏览器触发横幅显示事件
                window.console.log(e);
                this.prompt = true;
                this.deferred = e;
                //window.console.log(this.prompt);
                this.showAddToHomeScreen();
            }
    window.addEventListener("scroll", this.handleScroll, true);
    if (this.$cookies.isKey("Lang")){
      this.$i18n.locale=this.$cookies.get("Lang");
    
    }
  },
  methods: {
    showAddToHomeScreen(){
      if (localStorage.getItem("neverShowDialog")) {return 0}
      else {
        this.addtoscreendialog=true;
      }
    },
    neverShowDialog(){
        window.console.log("never");
        this.addtoscreendialog=false;
        localStorage.setItem("neverShowDialog", true);
    },
    addToHomescreen(){
        this.deferred.prompt();
        let _this=this
        this.deferred.userChoice
      .then(function (choiceResult) {
        if (choiceResult.outcome === 'accepted') {
          window.console.log('User accepted the A2HS prompt');
          _this.addtoscreendialog=false;
        } else {
          window.console.log('User dismissed the A2HS prompt');
        }
        // 释放不再有用的deferredPrompt对象
      });
      
    },
    handleScroll() {
      let scrolltop =
        document.documentElement.scrollTop || document.body.scrollTop;
      scrolltop > 30 ? (this.gotop = true) : (this.gotop = false);
    },
    toTop() {
      let top = document.documentElement.scrollTop || document.body.scrollTop;
      // 实现滚动效果
      const timeTop = setInterval(() => {
        document.body.scrollTop = document.documentElement.scrollTop = top -= 50;
        if (top <= 0) {
          clearInterval(timeTop);
        }
      }, 10);
    },
    See (e) {
        window.location.href = e
      }
    }
  
};
</script>
<style>
  .vercel-text {
    font-size: 18px;
  }
</style>