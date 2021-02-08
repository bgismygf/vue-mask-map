<template>
  <div id="app">
    <nav class="navbar navbar-expand-lg bg-white">
      <div class="container">
        <a class="navbar-brand font-w-bold nav-logo" href="#">
          <img src="./assets/img/logo.png" alt="">
          <h1 class="h5 d-inline-block font-w-bold align-middle mb-0 ml-2">口罩即時查</h1>
        </a>
        <button class="navbar-toggler" type="button" data-toggle="collapse"
          data-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent"
          aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav ml-auto">
            <li class="nav-item mr-3">
              <a class="nav-link text-main active"
                href="#">口罩供給現況 <span class="sr-only">(current)</span></a>
            </li>
            <!-- <li class="nav-item">
              <a class="nav-link text-main" href="#">口罩怎麼買</a>
            </li> -->
          </ul>
        </div>
      </div>
    </nav>
    <div class="container pt-3">
      <div class="row justify-content-center">
        <div class="col-12 col-md-10 col-lg-5">
          <div class="form-row">
            <div class="form-group col-6">
              <select id="inputState" class="form-control text-main" v-model="selectCity"
                @change="changeData()">
                <option disabled value="">全部縣市</option>
                <option v-for="(item, index) in taiwanCityData" :key="index" :value="item.CityName">
                  {{item.CityName}}
                </option>
              </select>
            </div>
            <div class="form-group col-6">
              <select id="inputState" class="form-control text-main" v-model="selectArea"
                @change="changeData()">
                <option disabled value="">全部區域</option>
                <option v-for="(item, index) in area" :key="index" :value="item.AreaName">
                  {{item.AreaName}}
                </option>
              </select>
            </div>
          </div>
            <div class="pl-2">
              <span class="h2 mr-2 font-w-bold text-main">口罩實名制</span>
              <span class="mr-2 text-main">購買日</span>
              <a href="#"><i class="fas fa-question-circle fa-lg text-muted"></i></a>
              <div class="d-flex mt-3 mb-3">
                <div class="mr-auto text-main">
                  <!-- <h6 class="small">距離方圓 5公里 以內的供應商</h6> -->
                  <h6 v-if="updated" class="small">資訊更新時間 {{updated}}</h6>
                </div>
                <button type="button"
                  class="btn btn-outline-main rounded-pill align-self-center px-3"
                    @click="getPharmacyData()">
                    <i class="fas fa-cog fa-spin fa-lg" v-if="status.refreshIcon"></i>
                    重整列表
                </button>
              </div>
            </div>
<!-- 藥局列表 -->
            <div class="scroll-content px-2 pt-1">
              <div v-for="(item, index) in pharmacyDataFilter" :key="index">
                <div class="jq-card b-radius-10px card-shadow bg-white pt-3 pb-1 px-3 mb-4"
                  v-if="index + 1 <= piece">
                <a href="#" class="text-decoration-none" @click="updateMarker(item)">
                  <div class="row">
                  <div class="col-6" style="padding-right: 7.5px;">
                    <div class="b-radius-10px text-white py-2 pl-2 position-relative"
                      :class="{
                                'bg-many' : item.properties.mask_adult >= 100,
                                'bg-median' : item.properties.mask_adult <= 99,
                                'bg-small' : item.properties.mask_adult == 0,
                              }">
                    <p class="small mb-0">成人口罩數量</p>
                    <div class="d-inline-block align-middle">
                      <span class="h2">{{item.properties.mask_adult}}</span>
                      <span class="small">片</span>
                    </div>
                    <i class="fas align-middle icon-control
                      position-absolute"
                      :class="{
                        'fa-check-circle' : item.properties.mask_adult >= 100,
                        'fa-exclamation-circle' : item.properties.mask_adult <= 99,
                        'fa-times-circle' : item.properties.mask_adult == 0,
                      }">
                      </i>
                  </div>
                  </div>
                  <div class="col-6" style="padding-left: 7.5px;">
                    <div class="b-radius-10px text-white py-2 pl-2 position-relative"
                      :class="{
                                'bg-many' : item.properties.mask_child >= 100,
                                'bg-median' : item.properties.mask_child <= 99,
                                'bg-small' : item.properties.mask_child == 0,
                              }">
                    <p class="small mb-0">兒童口罩數量</p>
                    <div class="d-inline-block align-middle">
                      <span class="h2">{{item.properties.mask_child}}</span>
                      <span class="small">片</span>
                    </div>
                    <i class="fas align-middle icon-control
                      position-absolute"
                      :class="{
                        'fa-check-circle' : item.properties.mask_child >= 100,
                        'fa-exclamation-circle' : item.properties.mask_child <= 99,
                        'fa-times-circle' : item.properties.mask_child == 0,
                      }">
                      </i>
                  </div>
                  </div>
                </div>
                <div class="row mt-3">
                  <div class="col-12">
                    <a href="#"></a>
                    <span class="font-weight-bold h6 mr-1 mb-1 text-main">
                      {{index +  1}}.{{item.properties.name}}
                      </span>
                    <!-- <span class="font-weight-bold small text-main">1.2 km</span> -->
                    <table class="table">
                      <tbody class="small">
                        <tr>
                          <td scope="col" width="10%" class="font-weight-bold text-main">地址</td>
                          <td scope="col" width="50%" class="text-small">
                            {{item.properties.address}}
                            </td>
                          <td scope="col" width="30%" class="text-right">
                          <a :href="`https://www.google.com.tw/maps/place/${item.properties.address}`" target="_blank" class="text-small">於地圖查看</a>
                          </td>
                        </tr>
                        <tr>
                          <td class="font-weight-bold text-main">電話</td>
                          <td class="text-small">{{item.properties.phone}}</td>
                          <td class="text-right">
                            <a :href="`tel:${item.properties.phone}`" class="text-small">撥打電話</a>
                          </td>
                        </tr>
                        <tr>
                          <td class="font-weight-bold text-main">備註</td>
                          <td colspan="2" class="text-small">{{item.properties.note}}</td>
                        </tr>
                      </tbody>
                    </table>
                  </div>
                </div>
                </a>
              </div>
            </div>

<!-- 顯示更多 -->
            <div class="d-flex justify-content-center">
              <i class="fas fa-spinner fa-spin fa-lg" v-if="status.icon"></i>
            </div>
          </div>
        </div>
        <div class="col-lg-7">
          <div class="d-none d-lg-block" id="map" style="height: calc(100vh - 115px)"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import $ from 'jquery';
import L from 'leaflet';

let osmMap = {};

export default {
  name: 'App',
  data: () => ({
    pharmacyData: {},
    taiwanCityData: {},
    selectCity: '',
    selectArea: '',
    showData: {},
    citydata: {},
    piece: 10,
    updated: '',
    status: {
      icon: false,
      refreshIcon: false,
    },
    quantityColor: {
      many: 'bg-many',
      median: 'bg-median',
      small: 'bg-small',
    },
  }),
  methods: {
    getPharmacyData() {
      const url = 'https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json?fbclid=IwAR1dnONo5ndjbYoiQOHymhawhbnRDFKmWVjQT4A5gV5Wo4zccyBvp0peAgk';
      this.status.refreshIcon = true;
      this.$http.get(url).then((response) => {
        this.pharmacyData = response.data.features;
        this.status.refreshIcon = false;
        // console.log(this.pharmacyData);
      });
    },
    getTaiwanCityData() {
      const url = 'https://raw.githubusercontent.com/donma/TaiwanAddressCityAreaRoadChineseEnglishJSON/master/CityCountyData.json';
      this.$http.get(url).then((response) => {
        this.taiwanCityData = response.data;
      });
    },
    bindPopupColor(quantity) {
      let status;
      if (quantity === 0) {
        return this.quantityColor.small;
      }
      if (quantity >= 100) {
        return this.quantityColor.many;
      }
      if (quantity <= 99) {
        return this.quantityColor.median;
      }
      return status;
    },
    updateMarker(item) {
      this.removeMapMarker();
      const adult = this.bindPopupColor(item.properties.mask_adult);
      const child = this.bindPopupColor(item.properties.mask_child);
      osmMap.panTo(new L.LatLng(item.geometry.coordinates[1], item.geometry.coordinates[0]));
      L.marker([item.geometry.coordinates[1], item.geometry.coordinates[0]]).addTo(osmMap).bindPopup(`<div class="d-flex justify-content-center text-white">
      <div class="${adult} p-1 mr-1 rounded">${item.properties.mask_adult}</div><div class="${child} p-1 rounded">${item.properties.mask_child}</div></div>`).openPopup();
      this.updated = item.properties.updated;
    },
    removeMapMarker() {
      osmMap.eachLayer((layer) => {
        if (layer instanceof L.Marker) {
          osmMap.removeLayer(layer);
        }
      });
    },
    changeData() {
      this.piece = 10;
      $('.scroll-content').scrollTop(0);
    },
  },
  computed: {
    area() {
      const vm = this;
      if (vm.selectCity !== '') {
        vm.selectArea = '';
        const areaText = vm.taiwanCityData.filter((item) => item.CityName === vm.selectCity);
        return areaText[0].AreaList;
      }
      vm.selectArea = '';
      return {};
    },
    pharmacyDataFilter() {
      const vm = this;
      if (vm.selectCity !== '' && vm.selectArea !== '') {
        return vm.pharmacyData.filter((item) => (
          item.properties.town === vm.selectArea && item.properties.county === vm.selectCity));
      }
      if (vm.selectCity !== '') {
        return vm.pharmacyData.filter((item) => item.properties.county === vm.selectCity);
      }
      return vm.pharmacyData;
    },
  },
  mounted() {
    osmMap = L.map('map', {
      center: [25.03, 121.55],
      minZoom: 8,
      maxZoom: 18,
      zoom: 16,
      zoomControl: true,
      attributionControl: false,
    });

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(osmMap);

    $('.scroll-content').scroll(() => {
      const card = document.querySelectorAll('.jq-card');
      let cardHeight = 0;
      const margin = this.piece * 30;
      const scrollPos = $('.scroll-content').scrollTop();
      const scrollHeight = $('.scroll-content').height() + 5;

      card.forEach((element) => {
        cardHeight += element.offsetHeight;
      });

      const cardHeightAll = cardHeight + margin + 5;
      const range = cardHeightAll - scrollHeight;

      if (this.piece === this.pharmacyDataFilter.length) {
        return;
      }

      if (scrollPos === range) {
        this.status.icon = true;
        setTimeout(() => {
          this.status.icon = false;
          this.piece += 10;
        }, 1500);
      }
    });
  },
  created() {
    this.getPharmacyData();
    this.getTaiwanCityData();
  },
};
</script>

<style lang="scss">
@import 'assets/scss/all.scss';
.scroll-content {
  overflow: auto;
  max-height: calc(100vh - 317px);
  margin-left: -10px;
  &::-webkit-scrollbar { width: 10px; }
  &::-webkit-scrollbar-track-piece{ background: #f1f1f1; border-radius: 10px; }
  &::-webkit-scrollbar-thumb { background: $main-color; border-radius: 10px; }
  &::-webkit-scrollbar-thumb:hover { background: $main-dark-color; }
}

.card-shadow {
  box-shadow: 0px 3px 10px #0000001A;
}

.icon-control {
  font-size: 53px;
  text-indent: 10%;
  overflow: hidden;
  white-space: nowrap;
  opacity: 0.2;
  right: 0px;
}

.leaflet-popup-content {
  width: auto;
  margin: 20px 4px 10px;
}
</style>
