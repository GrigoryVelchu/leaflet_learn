<template><div id="map-wrap" style="height: 100vh">
  <client-only>
    <l-map :zoom=13 :center="[53.008545,37.841087]">
      <l-tile-layer url="http://{s}.tile.osm.org/{z}/{x}/{y}.png"></l-tile-layer>
      <l-marker :lat-lng="[53.008545,37.841087]"></l-marker>
      <l-geo-json :geojson="geoJson"  :option-style="geoStyleOption"></l-geo-json>
    </l-map>
  </client-only>
</div>
</template>

<script>
  export default {
    name: 'index',
    data() {
      return {
        geoJson: null,
        geoJson1:null,
      }
    },
    computed: {
      geoOptions(){
        return{
          onEachFeature:this.onEachFeatureFunc
        }
      },
      onEachFeatureFunc(){
        return (feature,layer)=>{
          if(feature.properties.code){
            layer.bindTooltip(`<div>${feature.properties.code}</div>`, {permanent:true, sticky:"true"})
          }
        }
      },
      geoStyleOption(){
        return{weight:10}
      }
    },
    async created() {
      const response = await fetch("https://gis-api.admlr.lipetsk.ru/api/v1/borders")
      const response1 = await fetch("https://rawgit.com/gregoiredavid/france-geojson/master/regions/pays-de-la-loire/communes-pays-de-la-loire.geojson")
      const geoJson = await response.json()
      const geoJson1 = await response1.json()
      this.geoJson = geoJson
      this.geoJson1 = geoJson1
      console.log(this.geoJson.features[0].geometry.coordinates)
      console.log(this.geoJson1.features[0].geometry.coordinates)
    }
  }
</script>
<style>

</style>
