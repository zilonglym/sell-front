<template>
  <div>
    <template v-if="showHeader">
      <v-header :seller="seller"></v-header>
          <div id="search"  class="search-item">
            <input id="searchinput" type="text" style="border-radius: 20px;" class="search" value="" placeholder="搜索" v-model.trim="title"/>
          </div>
      <div class="tab border-1px">
        <div class="tab-item">
          <router-link to="/goods">商品</router-link>
        </div>
        <!-- <div class="tab-item">
          <router-link to="/ratings">评论</router-link>
        </div> -->
        <div class="tab-item">
          <router-link to="/seller">商家</router-link>
        </div>
      </div>
    </template>

    <router-view :seller="seller" :showHeader="showHeader"></router-view>

  </div>
</template>

<script type="text/ecmascript-6">
  import {urlParse} from 'common/js/util';
  import header from 'components/header/header.vue';
  const ERR_OK = 0;

  export default {
    data() {
      return {
        seller: {
          id: (() => {
            let queryParam = urlParse();
            return queryParam.id;
          })()
        },
        showHeader: true,
        title: ''
      };
    },
    methods: {
      setContent(){
        //console.log(this.title);
        this.$store.state.searchContent = this.title;
      },
      async fetchData(val) {
        this.$store.state.searchContent = this.title;
      },
      changeHash() {
        const hash = window.location.hash;
        if (hash.indexOf('payment') > -1
        || hash.indexOf('order') > -1) {
          this.showHeader = false;
        } else {
          this.showHeader = true;
        }
      }
    },
    watch: {
    //watch search change
      title: function() {
        //console.log(this.title);
        delay(() => {
          this.fetchData();
        }, 300);
      }
    },
    created() {
      this.changeHash();
      window.addEventListener('hashchange', () => {
        this.changeHash();
      });
      if (this.showHeader) {
        this.$http.get('/sell/api/seller.json').then((response) => {
          response = response.body;
          if (response.errno === ERR_OK) {
            this.seller = Object.assign({}, this.seller, response.data);
          }
        });
      }
    },
    components: {
      'v-header': header
    }
  };
    // 节流函数
  const delay = (function() {
    let timer = 0;
    return function(callback, ms) {
      clearTimeout(timer);
      timer = setTimeout(callback, ms);
    };
  })();
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "./common/stylus/mixin.styl"

  .search-item
    display: flex; 
    height:36px;
    border-1px(rgba(27, 17, 27, 0.1))
    .search
      text-align: center;
      border: 0px solid #444444;
      height:24px;
      margin: 6px 15px 6px 15px;
      width:100%;
      // padding-left:0px; 
      background-color:#F0F0F0;
      // background:url(images/Contact_05.jpg) no-repeat right top; 
      font-size:15px;
      font-weight: 500;

  .tab
    display: flex
    width: 100%
    height: 25px
    line-height: 25px
    // border-bottom: 1px solid rgba(7, 17, 27, 0.1)
    border-1px(rgba(7, 17, 27, 0.1))
    .tab-item
      flex: 1
      text-align: center
      & > a
        display: block
        font-size: 14px
        color: rgb(77, 85, 93)
        &.active
          color: rgb(240, 20, 20)
</style>
