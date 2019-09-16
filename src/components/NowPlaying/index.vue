<template>
  <div class="movie_body">
    <loading v-if="isloading"></loading>
    <Scroller v-else :handleToScroll="handleToScroll" :handleToTouchEnd="handleToTouchEnd">
      <ul>
        {{pullDownMsg}}
        <li v-for="list in movieList" :key="list.id">
          <div class="pic_show" @tap="handleToDetail">
            <img :src="list.img | setWH('128.180')" />
          </div>
          <div class="info_list">
            <h2>
              {{list.nm}}
              <img v-if="list.version" src="@/assets/maxs.png" alt />
            </h2>

            <p>
              观众评
              <span class="grade">{{list.sc}}</span>
            </p>
            <p>主演：{{list.star}}</p>
            <p>{{list.showInfo}}</p>
          </div>
          <div class="btn_mall">购票</div>
        </li>
      </ul>
    </Scroller>
  </div>
</template>

<script>
export default {
  name: "Nowplaying",
  data() {
    return {
      movieList: [],
      pullDownMsg: "",
      isloading: true,
      prevCityId: -1
    };
  },
  activated() {
    let id = this.$store.state.city.id;
    if (this.prevCityId === id) {
      return;
    } else {
      this.isloading = true;
      this.axios.get(`/api/movieOnInfoList?cityId=${id}`).then(res => {
        let msg = res.data.msg;
        if (msg === "ok") {
          this.movieList = res.data.data.movieList;
          this.isloading = false;
          this.prevCityId = id;
        }
      });
    }
  },
  methods: {
    handleToDetail() {
      console.log(123);
    },
    handleToScroll(pos) {
      if (pos.y > 30) {
        this.pullDownMsg = "正在更新中";
      }
    },
    handleToTouchEnd(pos) {
      if (pos.y > 30) {
        this.axios.get("/api/movieOnInfoList?cityId=11").then(res => {
          let msg = res.data.msg;
          if (msg === "ok") {
            this.pullDownMsg = "更新成功";
            this.movieList = res.data.data.movieList;
          }
        });
      }
      setTimeout(() => {
        this.pullDownMsg = "";
      }, 1000);
    }
  }
};
</script>
<style scoped>
#content .movie_body {
  flex: 1;
  overflow: auto;
}
.movie_body ul {
  margin: 0 12px;
  overflow: hidden;
}
.movie_body ul li {
  margin-top: 12px;
  display: flex;
  align-items: center;
  border-bottom: 1px #e6e6e6 solid;
  padding-bottom: 10px;
}
.movie_body .pic_show {
  width: 64px;
  height: 90px;
}
.movie_body .pic_show img {
  width: 100%;
}
.movie_body .info_list {
  margin-left: 10px;
  flex: 1;
  position: relative;
}
.movie_body .info_list h2 {
  font-size: 17px;
  line-height: 24px;
  width: 150px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
.movie_body .info_list p {
  font-size: 13px;
  color: #666;
  line-height: 22px;
  width: 200px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
.movie_body .info_list .grade {
  font-weight: 700;
  color: #faaf00;
  font-size: 15px;
}
.movie_body .info_list img {
  width: 50px;
  position: absolute;
  right: 10px;
  top: 5px;
}
.movie_body .btn_mall,
.movie_body .btn_pre {
  width: 47px;
  height: 27px;
  line-height: 28px;
  text-align: center;
  background-color: #f03d37;
  color: #fff;
  border-radius: 4px;
  font-size: 12px;
  cursor: pointer;
}
.movie_body .btn_pre {
  background-color: #3c9fe6;
}
</style>

