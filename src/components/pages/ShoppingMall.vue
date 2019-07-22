<template>
  <div>
    <div class="search-bar">
      <van-row>
        <van-col span="3">
          <img :src="locationIcon" width="80%" class="location-icon">
        </van-col>
        <van-col span="16">
          <input type="text" class="search-input">
        </van-col>
        <van-col span="5" class="button">
          <van-button size="mini">查找</van-button>
        </van-col>
      </van-row>
    </div>
    <div class="swiper-area">
      <van-swipe :autoplay="3000">
        <van-swipe-item v-for="( banner ,index) in bannerPicArray" :key="index">
          <img v-lazy="banner.image" width="100%">
        </van-swipe-item>
      </van-swipe>
    </div>
    <div class="type-bar">
      <div v-for="(cate,index) in category" :key="index">
        <img v-lazy="cate.image" width="90%">
        <span>{{cate.mallCategoryName}}</span>
      </div>
    </div>
    <div>
      <img v-lazy="adBanner" width="100%">
    </div>
    <div class="recommend-title">商品推荐</div>
    <div class="recommend-body">
      <swiper :options="swiperOption">
        <swiper-slide v-for="(item,index) in recommendGoods " :key="index">
          <div class="recommend-item">
            <img :src="item.image" width="80%">
            <div>{{item.goodsName}}</div>
            <div>￥{{item.price | moneyFilter}}(￥{{item.mallPrice | moneyFilter}})</div>
          </div>
        </swiper-slide>
      </swiper>
    </div>
    <div class="hot-title">热卖商品</div>
    <div class="hot-goods">
      <van-list>
        <van-row>
          <van-col span="12" v-for="(item , index) in hotGoods" :key="index">
            <div class="goods-info" @click="goGoodsPage(item.goodsId)">
              <div class="goods-image">
                <img v-lazy="item.image" width="80%">
              </div>
              <div class="goods-name">{{item.name}}</div>
              <div class="goods-price">￥{{item.price | moneyFilter }}</div>
            </div>
          </van-col>
        </van-row>
      </van-list>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import URL from "@/serviceAPI.config.js";
import "swiper/dist/css/swiper.css";
import { swiper, swiperSlide } from "vue-awesome-swiper";
import { toMoney } from "@/filter/moneyFilter.js";
export default {
  data() {
    return {
      locationIcon: require("../../assets/images/location.png"),
      bannerPicArray: [],
      category: [],
      adBanner: "",
      recommendGoods: [],
      swiperOption: {
        slidesPerView: 3
      },
      hotGoods: [] //热卖商品
    };
  },
  filters: {
    moneyFilter(money) {
      return toMoney(money);
    }
  },
  components: { swiper, swiperSlide },
  created() {
    axios({
      url: URL.getShopingMallInfo,
      method: "get"
    })
      .then(response => {
        console.log(response);
        if (response.status == 200) {
          this.bannerPicArray = response.data.data.slides;
          this.category = response.data.data.category;
          this.adBanner = response.data.data.advertesPicture.PICTURE_ADDRESS;
          this.recommendGoods = response.data.data.recommend;
          this.hotGoods = response.data.data.hotGoods;
        }
      })
      .catch(error => {
        console.log(error);
      });
  },
  methods: {
    goGoodsPage(goodsId) {
      this.$router.push({
        name: "Goods",
        query: { goodsId: goodsId }
      });
    }
  }
};
</script>

<style scoped>
.search-bar {
  /* line-height和height把它们的值设置的一样，文字就会在垂直方向居中 */
  height: 2.2rem;
  background-color: #e5017d;
  line-height: 2.2rem;
}
.location-icon {
  padding-top: 0.2rem;
  padding-left: 0.3rem;
}
.search-input {
  width: 100%;
  height: 1.3rem;
  border-top: 0px;
  border-left: 0px;
  border-right: 0px;
  border-bottom: 1px solid #fff;
  background-color: #e5017d;
  color: #fff;
}
.button {
  padding-left: 0.8rem;
}
.type-bar {
  display: flex;
}
.type-bar div {
  font-size: 12px;
  text-align: center;
  flex: 1;
}
.recommend-title {
  font-size: 14px;
  padding: 0.3rem;
  color: #e5017d;
}
.recommend-body {
  border: 1px solid #eee;
}
.recommend-item {
  border-right: 1px solid #eee;
  font-size: 12px;
  /* text-align: center水平居中 */
  text-align: center;
}
.hot-title {
  padding: 0.2rem;
}
.goods-info {
  border: 0.5px solid #eee;
  text-align: center;
}
.goods-name {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  font-size: 18px;
}
.goods-price {
  font-size: 18px;
}
</style>