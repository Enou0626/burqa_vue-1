<template>
  <div class="bg">
    <header-bar text="发现"></header-bar>
    <van-pull-refresh v-model="refresh" @refresh="onRefresh" class="refresh">
      <van-list v-model="loading" :finished="finished" @load="onLoad">
        <div v-for="(item,index) in list">
          <common-item-list :data="item" @itemClick="push(item.comicId)" />
        </div>
      </van-list>
    </van-pull-refresh>
  </div>
</template>

<script>
import { List, PullRefresh } from "vant";
import HeaderBar from "@/components/widget/header-bar";
import CommonItemList from "@/components/novel/view/common-item-list";
export default {
  components: {
    [PullRefresh.name]: PullRefresh,
    [List.name]: List,
    CommonItemList,
    HeaderBar
  },
  data() {
    return {
      refresh: false,
      loading: false,
      finished: false,
      page: 1,
      list: [],
      argValue: this.$route.query.argValue
    };
  },
  created() {
    this.getData();
  },
  methods: {
    push(comicId) {
      this.$router.push({
        path: "/comics-detail",
        query: { data: comicId + "" }
      }); //comicId为int类型.直接传递为空
    },
    onRefresh() {
      this.refresh = true;
      this.page = 1;
      this.getData();
    },
    onLoad() {
      this.loading = true;
      this.page++;
      this.getData();
    },
    getData() {
      this.get(
        "v3/appV3_3/ios/phone/list/commonComicList?argName=detect&argValue=12&sexType=3",
        {
          argValue: this.argValue,
          page: this.page
        }
      ).then(res => {
        this.refresh = false;
        this.loading = false;
        if (res.returnData != null && res.returnData.comics.length != 0) {
          if (this.refresh) {
            this.list = res.returnData.comics;
          } else {
            this.list.splice(this.list.length, 0, ...res.returnData.comics);
          }
        } else {
          this.finished = true;
        }
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.bg {
  background-color: white;
  .refresh{
    margin-top: 35px;
  }
  .works-title-item {
    padding: 10px;
    display: flex;
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
}
</style>

