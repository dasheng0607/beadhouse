<template>
  <div id="login">
    <div class="top-info" :style="{height:navbarTop +'px'}"></div>
    <wux-navbar theme="dark" class="top">
      <view class="top-title">登录</view>
    </wux-navbar>
    <div class="center">
      <wux-cell-group>
        <wux-cell hover-class="none">
          <wux-input
            clear
            label="手机号"
            controlled
            type="number"
            :value="phone"
            @change="onChange"
            @clear="onClear"
            placeholder="请输入手机号码"
            maxlength="11"
          />
        </wux-cell>
      </wux-cell-group>
      <wux-button
        block
        type="positive"
        open-type="getPhoneNumber"
        lang="zh_CN"
        @getphonenumber="getPhoneNumber"
      >使用本机号码</wux-button>
      <wux-button
        block
        type="positive"
        open-type="getUserInfo"
        lang="zh_CN"
        @getuserinfo="onGotUserInfo"
      >授权登录</wux-button>
    </div>
    <wux-toast id="wux-toast" />
  </div>
</template>

<script>
import { formatTime } from "@/utils/index";
import { $wuxToast } from "../../../static/wux/index";

export default {
  components: {},

  data() {
    return {
      navbarTop: "20",
      phone: "18814125274"
    };
  },
  created() {
    
    wx.login({
      //获取code
      success: function(res) {
        wx.getUserInfo({
          success: function(res) {
            console.log("res2", res);
          }
        });
      }
    });
    try {
      const res = wx.getSystemInfoSync();
      this.navbarTop = res.statusBarHeight;
    } catch (e) {}
  },

  methods: {
    getPhoneNumber(e) {
      console.log(111111, e);
    },
    onGotUserInfo(e) {
      if(e.target.userInfo){
        this.globalData.userInfo = e.target.userInfo;
        this.globalData.userInfo.telePhone = this.phone;
        if (/^1(3|4|5|6|7|8|9)\d{9}$/.test(this.phone)) {
          $wuxToast().show({
            type: "success",
            duration: 1500,
            color: "#fff",
            text: "授权成功",
            success: () => {
               mpvue.switchTab({ url:'/pages/user/main' })
            }
          });
        } else {
          $wuxToast().show({
            type: "forbidden",
            duration: 1500,
            color: "#fff",
            text: "请输入正确手机号码",
            success: () => {
              console.log("手机号码错误");
            }
          });
        }
      }
    },
    onChange(e) {
      this.phone = e.target.value;
    },
    onClear(e) {
      console.log("onclear", e);
    }
  }
};
</script>

<style lang="less">
#login {
  height: 100vh;
  background-image: url("https://7465-te-651663-1257727594.tcb.qcloud.la/back2.png?sign=79bca63b8d2a84e564c9ed5ef2ce1909&t=1550568007");
  background-size: 100% 100%;
  background-repeat: no-repeat;
  .top-info {
    background-color: transparent;
  }
  .top-title {
    height: 44px;
    line-height: 44px;
  }
  .wux-navbar--dark {
    background-color: transparent;
  }
  .center {
    height: 80vh;
    padding: 0 10vw;
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
    ._wux-button {
      width: 100%;
    }
  }
}
</style>
