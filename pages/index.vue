<template><div id="map-wrap" style="height: 100vh">
  <client-only>
    <l-map :zoom=8 :center="centerMap"  style="{width: 100%}" @update:center="writeCenter">

      <l-control>
        <button @click="topo=!topo">Click to change map</button>
      </l-control>
      <l-tile-layer v-if="!topo" url="http://{s}.tile.osm.org/{z}/{x}/{y}.png"></l-tile-layer>
      <l-tile-layer v-else url="https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png"></l-tile-layer>

      <l-marker :lat-lng="[52.6031,39.5708]"></l-marker>
      <l-polygon color="black" :lat-lngs="latLngsPoligon"></l-polygon>
      <l-geo-json v-if="false" :geojson="geoJson"  :options-style="geoStyleOption" :options="geoOptions"></l-geo-json>

    </l-map>
  </client-only>
</div>
</template>

<script>

  export default {
    name: 'index',
    data() {
      return {
        centerMap:[52.6031,39.5708],
        geoJson: null,
        topo:false,
        maxBounds: [
          [51.80289371957734, 37.40707869686964],
          [53.72958979032285, 41.180864748521394]
        ],
        latLngsPoligon:[[],[]],
      }
    },
    methods:{
      async getGeoJson(url,latlngPol){
        const response = await fetch(url)
        const geoJson = await response.json()
        if(latlngPol){return geoJson.features[0].geometry.coordinates}
        let arrOfCoords = geoJson.features[0].geometry.coordinates.map(el=>el.reverse())
        const newGeoJson = JSON.parse(JSON.stringify(geoJson))
        newGeoJson.features[0].geometry.coordinates = [[]]
        newGeoJson.features[0].geometry.coordinates[[0]] = arrOfCoords
        return newGeoJson
      },
      writeCenter(e){console.log(e)}
    },
    computed: {
      geoOptions(){
        return{
          onEachFeature:this.onEachFeatureFunc
        }
      },
      onEachFeatureFunc(){
        return (feature,layer)=>{
          if(feature.properties.id){
            layer.bindTooltip(`<div>${feature.properties.id}</div>`, {permanent:false, sticky:"true"})
          }
        }
      },
      geoStyleOption(){
          return{
            weight:3,
            color:`rgba(0,0,0,0.5)`,
            fillColor:`rgba(0,0,0,0.3)`
          }
      },
    },
    async created() {
      this.latLngsPoligon = await this.getGeoJson("https://gis-api.admlr.lipetsk.ru/api/v1/borders", true)
      this.geoJson = await this.getGeoJson("https://gis-api.admlr.lipetsk.ru/api/v1/borders", false)
    }
  }
</script>
<style>

</style>
