<template>
  <div class="novel-page">
    <div class="swipe-wrapper">
      <mt-swipe :auto="4000">
        <mt-swipe-item v-for="(image, index) in galleryItems" :key="index">
          <img :src="image.cover" class="banner-img" @click="click(image)" />
        </mt-swipe-item>
      </mt-swipe>
    </div>
    <div v-for="(item,i) in comicLists">
      <!--排行,vip,订阅等-->
      <div v-if="item.comicType == 11">
        <div class="tag-item">
          <div v-for="(tagItem,index) in item.comics">
            <img :src="tagItem.cover" class="tag-img" @click="tabClick(index)">
          </div>
        </div>
      </div>
      <!--超人气作品-->
      <div v-else-if="item.itemTitle == '超人气作品'" class="popular-works">
        <div class="works-title-item" @click="more(item.argValue)">
          <div class="works-title">{{item.itemTitle}}</div>
          <div class="works-des">{{item.description}}
            <i class="iconfont icon-more"></i>
          </div>
        </div>
        <popular-works-item v-for="subItem in item.comics" :key="subItem.cover" :data="subItem" @itemClick="push(subItem.comicId)" />
      </div>
      <!--新作推送-->
      <div v-else class="popular-works">
        <div class="works-title-item" @click="more(item.argValue)">
          <div class="works-title">{{item.itemTitle}}</div>
          <div class="works-des">{{item.description}}
            <i class="iconfont icon-more"></i>
          </div>
        </div>
        <div v-if="item.itemTitle == '活动'">
          <common-item :data="item.comics[0]" @itemClick="push(subItem.comicId)" />
        </div>
        <div v-else>
          <div v-for="(subItem,index) in item.comics">
            <common-item v-if="subItem.cover.includes('ubig') && index == 0" :data="subItem" @itemClick="push(subItem.comicId)" />
            <common-item-count v-else :data="subItem " @itemClick="push(subItem.comicId)" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Swipe, SwipeItem } from "mint-ui";
import PopularWorksItem from "@/components/novel/view/popular-works-item";
import CommonItem from "@/components/novel/view/common-item";
import CommonItemCount from "@/components/novel/view/common-item-count";
import CrossLine from "@/components/widget/cross-line";
export default {
  components: {
    CommonItem,
    CrossLine,
    CommonItemCount,
    PopularWorksItem
  },
  created() {
    this.getData();
  },
  data() {
    return {
      galleryItems: [],
      comicLists: []
    };
  },
  methods: {
    getData() {
      this.get("/v3/appV3_3/ios/phone/comic/getDetectList").then(res => {
        this.galleryItems = res.returnData.galleryItems;
        this.comicLists = res.returnData.comicLists;
      });
    },
    push(comicId) {
      this.$router.push({
        path: "/comics-detail",
        query: { data: comicId + "" }
      }); //comicId为int类型.直接传递为空
    },
    more(argValue) {
      this.$router.push({ path: "/common", query: { argValue: argValue } });
    },
    tabClick(index) {
      if (index == 0) {
        this.$router.push({ path: "/rank" });
      } else if (index == 1) {
        this.$router.push({ path: "/vip" });
      } else if (index == 2) {
        this.$router.push({ path: "/subscibe" });
      } else if (index == 3) {
        this.$router.push({ path: "/common", query: { argValue: "12" } });
      } else if (index == 4) {
        this.$router.push({ path: "/classify" });
      }
    },
    click(img) {
      if (img.linkType == 3) {
        this.push(img.ext[0].val);
      } else {
        this.$router.push({ path: "/html-page" });
      }
    },
    video() {
      this.$toast(video);
    }
  }
};
</script>

<style lang="scss">
.swipe-wrapper {
  width: 100%;
  height: 220px;
}
.banner-img {
  width: 100%;
}
.tag-item {
  display: flex;
  flex-direction: row;
}
.tag-img {
  height: 100%;
  width: 100%;
}
.popular-works {
  flex: 1;
  overflow: hidden;
  // background-color: white;
}
.works-title-item {
  margin-top: 10px;
  padding: 10px;
  display: flex;
  background-color: white;
  flex-direction: row;
  .works-title {
    flex: 1;
    text-align: center;
  }
  .works-des {
    color: gray;
    font-size: 12px;
    float: right;
  }
}
.novel-page {
  margin-bottom: 20px;
}
</style>

