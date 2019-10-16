<template>
  <div id="user">
    <wux-card full title="用户信息" thumb-style="border-radius: 50%;" :thumb="userInfo.avatarUrl">
      <view slot="body">
        <wux-row>
          <wux-col span="6">
            绑定手环：
            <wux-button
              clear
              :bordered="false"
              :type="isBind ? 'balanced':'assertive'"
              @click="bindHand"
            >{{isBind ? '已绑定':'未绑定'}}</wux-button>
          </wux-col>
          <wux-col span="6">
            <view class="placeholder">
              手环电量：
              <wux-button
                clear
                :bordered="false"
                type="balanced"
              >{{isBind ? handInfo.power +' %': ''}}</wux-button>
            </view>
          </wux-col>
          <wux-col span="12">
            <view class="placeholder">
              快速拨号：
              <wux-button
                v-if="isBind"
                clear
                :bordered="false"
                :type="handInfo.phone ? 'balanced':'assertive'"
                @click="setUrgent"
              >{{ handInfo.phone || '未设置' }}</wux-button>
            </view>
          </wux-col>
        </wux-row>
      </view>
    </wux-card>
    <view class="news">
    <!-- <wux-tabs wux-class="bordered" controlled :current="current" @change="onTabsChange">
      <block v-for="(item,index) in tabs" :key="index">
        <wux-tab :key="item.key" :title="item.title">
        </wux-tab>
      </block>
    </wux-tabs> -->
    <wux-segmented-control theme="balanced" :values="segmented" @change="changeSegmented"/>
    <swiper :current="currentIndex" >
      <block v-for="(item,index) in tabs" :key="index">
        <swiper-item>
          <view class="content">{{ item.content }}</view>
        </swiper-item>
      </block>
    </swiper>
    </view>
     <view class="page__bd">
        <view class="weui-tab">
            <view class="weui-navbar">
                <block wx:for-items="{{ tabs}}" wx:key="{{ index }}">
                    <view data-id="{{ index }}" class="weui-navbar__item {{ activeIndex == index ? 'weui-bar__item_on' : '' }}" bindtap="tabClick">
                        <view class="weui-navbar__title">{{ item }}</view>
                    </view>
                </block>
                <view class="weui-navbar__slider" style="left: {{ sliderLeft }}px; transform: translateX({{ sliderOffset }}px); -webkit-transform: translateX({{ sliderOffset }}px);"></view>
            </view>
            <view class="weui-tab__panel">
                <view class="weui-tab__content" hidden="{{ activeIndex !== 0 }}">
                    <wux-prompt visible="{{ activeIndex === 0 }}" title="{{ msg1.title }}" text="{{ msg1.text }}" />
                </view>
                <view class="weui-tab__content" hidden="{{ activeIndex !== 1 }}">
                    <wux-prompt visible="{{ activeIndex === 1 }}" icon="{{ msg2.icon }}" title="{{ msg2.title }}" text="{{ msg2.text }}" buttons="{{ msg2.buttons }}" bind:click="buttonClicked" />
                </view>
                <view class="weui-tab__content" hidden="{{ activeIndex !== 2 }}">
                    <wux-prompt visible="{{ activeIndex === 2 }}" icon="{{ msg3.icon }}" title="{{ msg3.title }}" />
                </view>
            </view>
        </view>
    </view>
    <wux-toast id="wux-toast" />  
    <wux-dialog id="wux-dialog" />
  </div>
</template>

<script>
import { $wuxToast, $wuxDialog } from "../../../static/wux/index";

export default {
  data() {
    return {
      isBind: true,
      userInfo: {},
      handInfo: {
        power: 50,
        phone: "18814125274"
      },
      tabs: [
        {
            key: 'tab1',
            title: '通知信息',
            content: 'Content of tab 1',
        },
        {
            key: 'tab2',
            title: '警告信息',
            content: 'Content of tab 2',
        }
      ],
      current: 'tab1',
      currentIndex: '0',
      segmented:['通知信息', '警告信息']
    };
  },

  components: {},

  methods: {
    bindHand() {
      if (this.isBind) return;
      this.isBind = true;
      $wuxToast().show({
        type: "success",
        duration: 1500,
        color: "#fff",
        text: "绑定成功"
      });
      // if (mpvuePlatform === 'wx') {
      //   mpvue.switchTab({ url })
      // } else {
      //   mpvue.navigateTo({ url })
      // }
    },
    setUrgent() {
      $wuxDialog().prompt({
        resetOnClose: true,
        title: "设置快速拨号",
        // content: '请输入11位手机号码',
        fieldtype: "number",
        defaultText: this.handInfo.phone * 1,
        placeholder: "请输入11位手机号码",
        maxlength: 11,
        onConfirm: (e, response) => {
          if (/^1(3|4|5|6|7|8|9)\d{9}$/.test(response)) {
            this.handInfo.phone = response;
            $wuxToast().show({
              type: "success",
              duration: 1500,
              color: "#fff",
              text: "设置成功"
            });
          } else {
            $wuxToast().show({
              type: "forbidden",
              duration: 1500,
              color: "#fff",
              text: "请输入正确手机号码"
            });
          }
        }
      });
    },
    onTabsChange(e) {
        console.log('onTabsChange', e)
        const { key } = e.target
        const index = this.tabs.map((n) => n.key).indexOf(key)
        this.current = key;
        this.currentIndex = index;
    },
    changeSegmented(e) {
      console.log(e);
      const { key } = e.target
      this.currentIndex = key;
    }
  },

  mounted() {
    this.userInfo = this.globalData.userInfo;
    // let app = getApp()
    console.log(1111111111, this.globalData.userInfo.avatarUrl);
  }
};
</script>

<style scoped>
.userinfo {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.news {
  padding: 10px;
}
</style>
