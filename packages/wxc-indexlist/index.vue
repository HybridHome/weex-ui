<!-- CopyRight (C) 2017-2022 Alibaba Group Holding Limited. -->
<!-- Created by Tw93 on 16/11/02. -->
<!-- Update by Tw93 on 17/10/13. -->
<!--A index list. -->

<template>
  <div class="wxc-index-list">
    <list class="index-list"
          :style="{height: height+'px'}">
      <cell v-for="(v,i) in formatList"
            :key="i"
            :ref="'index-item-title-' + v.title">
        <text :class="['index-list-title',v.type && v.type=='group' && 'group-title']"
              v-if="!onlyShowList">{{v.title}}</text>
        <div v-if="v.type && v.type=='group' && !onlyShowList"
             class="group">
          <div v-for="(group,index) in v.data"
               :key="index"
               class="group-list">
            <div v-for="(item,i) in group"
                 :key="i"
                 @click="itemClicked(item)"
                 class="group-item">
              <image v-if="item.isLocation"
                     class="location-icon"
                     src="//gw.alicdn.com/tfs/TB1JUiUPFXXXXXUXXXXXXXXXXXX-32-32.png"></image>
              <div class="item-content">
                <text class="item-name">{{item.name}}</text>
                <text class="item-desc"
                      v-if="item.desc">{{item.desc}}</text>
              </div>
            </div>
          </div>
        </div>
        <div v-if="v.type ==='list'">
          <div class="index-list-item"
               v-for="(item,index) in v.data"
               :key="index"
               @click="itemClicked(item)">
            <text class="title">{{item.name}}</text>
            <text class="desc">{{item.desc}}</text>
          </div>
        </div>
      </cell>
    </list>
    <div class="index-list-nav"
         :style="navStyle"
         v-if="showIndex && !onlyShowList">
      <text v-for="(item,index) in formatList"
            :key="index"
            :title="item.title"
            @click="go2Key(item.title)"
            class="list-nav-key">{{item.title}}</text>
    </div>
    <div class="index-list-pop"
         v-if="popKeyShow">
      <text class="list-pop-text">{{popKey}}</text>
    </div>
  </div>
</template>

<script>
  const dom = weex.requireModule('dom');
  const Util = require('./util');
  module.exports = {
    props: {
      height: {
        type: [Number, String],
        default: Util.getPageHeight()
      },
      normalList: {
        type: Array,
        default: () => ([])
      },
      onlyShowList: {
        type: Boolean,
        default: false
      },
      showIndex: {
        type: Boolean,
        default: true
      },
      navStyle: {
        type: Object,
        default: () => ({})
      },
      hotListConfig: {
        type: Object,
        default: () => ({})
      },
      // 城市选择子组件 特殊情况支持
      cityLocationConfig: {
        type: Object,
        default: () => ({})
      }
    },
    computed: {
      formatList () {
        const { normalList, hotListConfig, cityLocationConfig } = this;
        return Util.formatTotalList(normalList, hotListConfig, cityLocationConfig);
      }
    },
    data: () => ({
      popKeyShow: false,
      popKey: '',
      navOffsetY: 0,
      timer: null
    }),
    methods: {
      itemClicked (item) {
        this.$emit('wxcIndexlistItemClicked', {
          item
        });
      },
      go2Key (key) {
        const keyEl = this.$refs['index-item-title-' + key][0];
        dom.scrollToElement(keyEl, {
          offset: 0
        });
        this.popKey = key;
        this.popKeyShow = true;
        this.timer && clearTimeout(this.timer);
        this.timer = setTimeout(() => {
          this.popKeyShow = false;
        }, 600);
      }
    }
  };
</script>

<style scoped>
  .wxc-index-list {
    position: relative;
  }

  .index-list {
    width: 750px;
    height: 1334px;
  }

  .index-list-title {
    border-bottom-width: 1px;
    border-color: rgba(32, 35, 37, 0.15);
    background-color: #FBFBFB;
    font-size: 24px;
    color: #666666;
    padding-bottom: 14px;
    padding-top: 14px;
    padding-left: 23px;
    width: 750px;
  }

  .group-title {
    border-bottom-width: 0;
    padding-bottom: 0;
    padding-top: 24px;
  }

  .index-list-item {
    width: 750px;
    flex-direction: row;
    align-items: center;
    border-bottom-width: 1px;
    border-bottom-color: #e0e0e0;
    height: 92px;
    padding-left: 24px;
    padding-right: 24px;
    background-color: #FFFFFF;
  }

  .title {
    font-size: 32px;
    color: #3D3D3D;
  }

  .desc {
    font-size: 24px;
    color: #A5A5A5;
    margin-left: 30px;
  }

  .index-list-nav {
    position: absolute;
    top: 0;
    right: 0;
    margin-bottom: 60px;
    margin-top: 60px;
    padding-bottom: 20px;
    padding-top: 20px;
    width: 70px;
  }

  .list-nav-key {
    text-align: center;
    font-size: 24px;
    height: 40px;
    color: #666666;
  }

  .index-list-pop {
    position: fixed;
    top: 550px;
    left: 316px;
    width: 120px;
    height: 120px;
    text-align: center;
    justify-content: center;
    background-color: rgba(32, 35, 37, .6);
    border-bottom-left-radius: 60px;
    border-bottom-right-radius: 60px;
    border-top-left-radius: 60px;
    border-top-right-radius: 60px;
    padding-left: 0;
    padding-right: 0;
    padding-top: 35px;
    padding-bottom: 35px;
    color: #ffffff;
  }

  .list-pop-text {
    font-size: 40px;
    text-align: center;
    color: #ffffff;
  }

  .group {
    padding-bottom: 18px;
    padding-right: 70px;
    background-color: #FBFBFB;
  }

  .group-list {
    flex-direction: row;
    margin-left: 18px;
    margin-top: 18px;
    background-color: #FBFBFB;
  }

  .group-item {
    width: 146px;
    height: 64px;
    border-width: 1px;
    border-color: #e0e0e0;
    margin-right: 18px;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    background-color: #FFF;
  }

  .item-content {
    flex-direction: column;
  }

  .item-name {
    font-size: 24px;
    line-height: 26px;
    color: #333;
  }

  .item-desc {
    margin-top: 2px;
    color: #999;
    font-size: 20px;
    text-align: center;
  }

  .location-icon {
    width: 32px;
    height: 32px;
    margin-right: 8px;
  }
</style>
