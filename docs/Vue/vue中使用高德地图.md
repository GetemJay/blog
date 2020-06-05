# 如何在vue中使用高德地图

### 首先在main.js中引入并初始化
```js
import VueAMap from 'vue-amap'
Vue.use(VueAMap)
// 初始化
VueAMap.initAMapApiLoader({
  key: '****************', // 这是key
  plugin: ['Geolocation', 'ToolBar'], // 用到的插件
  v: '1.4.4'
})
```

### 写个搜索地名的输入框组件

```js
import { createCustomComponent } from 'vue-amap'

const customMapSearchbox = createCustomComponent({
  props: {
    input: String
  },
  init (options, map) {
    return new Promise(resolve => {
      AMap.plugin(['AMap.Autocomplete', 'AMap.PlaceSearch'], () => {
        const autocomplete = new AMap.Autocomplete(options)
        const placeSearch = new AMap.PlaceSearch()
        AMap.event.addListener(autocomplete, 'select', (e) => {
          placeSearch.search(e.poi.name, (status, result) => {
            this.$emit('select', result.poiList.pois)
          })
        })
        resolve(autocomplete)
      })
    })
  },
  contextReady () {
  }
})
export default customMapSearchbox
```

### 如何使用

```vue
<template>
  <div class="container">
    <div class="tip">
      <input class="custom-componet-input" id="custom-componet-input" v-model="inputValue"/>
    </div>
    <el-amap :plugin="plugin" :zoom="zoom" :center="center" ref="amap" :events="mapEvent">
      <custom-map-searchbox @select="selectSearch" input="custom-componet-input"></custom-map-searchbox>
      <el-amap-marker
        v-if="markers.length"
        v-for="(marker, index) in markers"
        :position="marker.position"
        :events="marker.events"
        :title="marker.title"
        :clickable="true"
        :key="index">
      </el-amap-marker>
    </el-amap>
  </div>
</template>

<script>
import customMapSearchbox from '../utils/customMapSearchbox'

export default {
  data () {
    const self = this
    return {
      markers: [],
      center: [0, 0],
      currentCenter: [], // 当前坐标
      zoom: 14,
      plugin: [{
        pName: 'Geolocation',
        events: {
          init (o) {
            // o 是高德地图定位插件实例
            o.getCurrentPosition((status, result) => {
              if (result && result.position) {
                self.currentCenter = [result.position.lng, result.position.lat]
                self.center = [...self.currentCenter]
                self.loaded = true
                self.$nextTick()
              }
            })
          },
          complete () {}
        }
      }, 'ToolBar'],
      inputValue: '',
      mapEvent: {
        // 点击地图上的热点
        hotspotclick (e) {
          let { lng, lat } = e.lnglat
          const geocoder = new AMap.Geocoder({
            radius: 1000
          })
          // 根据坐标获取详细地址
          geocoder.getAddress([lng, lat], (status, result) => {
            if (status === 'complete' && result.info === 'OK') {
              if (result && result.regeocode) {
                self.$emit('select', {
                  name: e.name,
                  address: result.regeocode.formattedAddress,
                  location: e.lnglat
                })
                self.$nextTick()
              }
            }
          })
        }
      }
    }
  },
  methods: {
    selectSearch (pois) {
      this.markers = []
      const self = this
      let latSum = 0
      let lngSum = 0
      pois.forEach(poi => {
        let {lng, lat} = poi.location
        lngSum += lng
        latSum += lat
        let position = [lng, lat]
        let markerObj = {
          position,
          title: poi.name,
          events: {
            click () {
              self.$emit('select', {
                name: poi.name,
                address: poi.address,
                location: poi.location
              })
              self.center = [lng, lat]
            }
          }
        }
        this.markers.push(markerObj)
      })
      if (pois.length === 1) {
        this.$emit('select', {
          name: pois[0].name,
          address: pois[0].address,
          location: pois[0].location
        })
      }
      let center = {
        lng: lngSum / pois.length,
        lat: latSum / pois.length
      }
      this.center = [center.lng, center.lat]
    },
    reset () {
      const mapInstance = this.$refs.amap.$$getInstance()
      mapInstance.setZoomAndCenter(this.zoom, this.currentCenter)
      this.markers = []
      this.inputValue = ''
    }
  },
  components: {
    customMapSearchbox
  }
}
</script>

<style>
  .container {
    position: relative;
    width: 100%;
    height: 500px;
  }
  .tip {
    background-color: #ddf;
    color: #333;
    border: 1px solid silver;
    box-shadow: 3px 4px 3px 0 silver;
    position: absolute;
    top: 10px;
    right: 10px;
    border-radius: 5px;
    overflow: hidden;
    line-height: 20px;
    z-index: 99;
  }
  .tip input {
    height: 25px;
    border: 0;
    padding-left: 5px;
    width: 280px;
    border-radius: 3px;
    outline: none;
  }
</style>
```

<back-to-top />

