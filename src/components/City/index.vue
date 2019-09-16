<template>
  <div class="city_body">
    <div class="city_list">
      <loading v-if="isLoading"></loading>
      <Scroller v-else ref="city_List">
        <div>
          <div class="city_hot">
            <h2>热门城市</h2>
            <ul class="clearfix">
              <li
                v-for="hot in hotCity"
                :key="hot.id"
                @tap="handleToCity(hot.nm,hot.id)"
              >{{hot.nm}}</li>
            </ul>
          </div>
          <div class="city_sort" ref="city">
            <div v-for="list in cityList" :key="list.id">
              <h2>{{list.index}}</h2>
              <ul>
                <li v-for="nm in list.list" :key="nm.id" @tap="handleToCity(nm.nm,nm.id)">{{nm.nm}}</li>
              </ul>
            </div>
          </div>
        </div>
      </Scroller>
    </div>
    <div class="city_index">
      <ul>
        <li
          v-for="(item,index) in cityList"
          :key="item.index"
          @touchstart="handleToIndex(index)"
        >{{item.index}}</li>
      </ul>
    </div>
  </div>
</template>
<script>
export default {
  name: "City",
  mounted() {
    let getcityList = localStorage.getItem("cityList");
    let gethotCity = localStorage.getItem("hotCity");
    if (getcityList && gethotCity) {
      this.cityList = JSON.parse(getcityList);
      this.hotCity = JSON.parse(gethotCity);
      this.isLoading = false;
    } else {
      this.axios.get("/api/cityList").then(res => {
        let msg = res.data.msg;
        if (msg === "ok") {
          let cities = res.data.data.cities;
          let { cityList, hotCity } = this.formatCityList(cities);
          this.cityList = cityList;
          this.hotCity = hotCity;
          this.isLoading = false;
          localStorage.setItem("cityList", JSON.stringify(cityList));
          localStorage.setItem("hotCity", JSON.stringify(hotCity));
        }
      });
    }
  },
  data() {
    return {
      hotCity: [],
      cityList: [],
      isLoading: true
    };
  },
  methods: {
    formatCityList(cities) {
      let cityList = [],
        hotCity = [];

      function toCom(py) {
        for (let i = 0; i < cityList.length; i++) {
          if (cityList[i].index === py) {
            return false;
          }
        }
        return true;
      }

      cities.forEach(val => {
        let obj = {};
        let py = val.py.substring(0, 1).toUpperCase();

        if (toCom(py)) {
          cityList.push({ index: py, list: [val] });
        } else {
          for (let j = 0; j < cityList.length; j++) {
            if (cityList[j].index === py) {
              cityList[j].list.push(val);
            }
          }
        }

        // let flog = false;

        // if (flog === false) {
        //   obj.index = py;
        //   obj.list = [];
        //   obj.list.push(val);
        //   cityList.push(obj);
        // }
      });

      cities.forEach(val => {
        if (val.isHot) {
          hotCity.push(val);
        }
      });

      cityList.sort((a, b) => {
        if (a.index > b.index) {
          return 1;
        } else if (a.index < b.index) {
          return -1;
        } else {
          return 0;
        }
      });

      return { cityList, hotCity };
    },
    handleToIndex(index) {
      let city = this.$refs.city;
      let h2 = city.getElementsByTagName("h2");
      // city.parentNode.scrollTop = h2[index].offsetTop;
      this.$refs.city_List.toScrollTop(-h2[index].offsetTop);
    },
    handleToCity(nm, id) {
      this.$store.commit("city/CITY_INFO", { nm, id });
      localStorage.setItem("nowNm", nm);
      localStorage.setItem("nowId", id);
      this.$router.push("/movie/nowPlaying");
    }
  }
};
</script>

<style scoped>
#content .city_body {
  margin-top: 45px;
  display: flex;
  width: 100%;
  position: absolute;
  top: 0;
  bottom: 0;
}
.city_body .city_list {
  flex: 1;
  overflow: auto;
  background: #fff5f0;
}
.city_body .city_list::-webkit-scrollbar {
  background-color: transparent;
  width: 0;
}
.city_body .city_hot {
  padding-top: 20px;
}
.city_body .city_hot h2 {
  padding-left: 15px;
  line-height: 30px;
  font-size: 14px;
  background: #f0f0f0;
  font-weight: normal;
}

.city_body .city_hot ul li {
  float: left;
  background: #fff;
  width: 29%;
  height: 33px;
  margin-top: 15px;
  margin-left: 3%;
  padding: 0 4px;
  border: 1px solid #e6e6e6;
  border-radius: 3px;
  line-height: 33px;
  text-align: center;
  box-sizing: border-box;
}

.city_body .city_sort div {
  margin-top: 20px;
}
.city_body .city_sort h2 {
  padding-left: 15px;
  line-height: 30px;
  font-size: 14px;
  background: #f0f0f0;
  font-weight: normal;
}
.city_body .city_sort ul {
  padding-left: 10px;
  margin-top: 10px;
}
.city_body .city_sort ul li {
  line-height: 30px;
  line-height: 30px;
}
.city_body .city_index {
  width: 20px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: center;
  border-left: 1px #e6e6e6 solid;
}
</style>
