<template>
    <div>
        <div>
            <span v-if="loading">Loading...</span>
            <label for="checkbox">Hiển thị viền</label>
            <input id="checkbox" v-model="show" type="checkbox">
            <input v-model="fillColor" type="color">
            <br>
        </div>
        <l-map :zoom="zoom" :center="center" @click="addMarker" style="height: 500px; width: 100%">
            <l-tile-layer :url="url" :attribution="attribution" />
            <l-geo-json v-if="show" :geojson="geojson" :options="options" :options-style="styleFunction" />
            <l-marker v-for="markers, index in marker" :key="markers" @click="removeMarker(index)" :lat-lng="markers" />
        </l-map>

    </div>
</template>

<script>
import { latLng, Icon } from "leaflet";
import { LMap, LTileLayer, LMarker, LGeoJson } from "vue2-leaflet";
delete Icon.Default.prototype._getIconUrl;
Icon.Default.mergeOptions({
    iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
    iconUrl: require('leaflet/dist/images/marker-icon.png'),
    shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
});

export default {
    name: "Geo",
    components: {
        LMap,
        LTileLayer,
        LGeoJson,
        LMarker
    },
    data() {
        return {
            loading: false,
            show: true,
            enableTooltip: true,
            zoom: 9,
            center: [21.030653, 105.847130],
            geojson: null,
            fillColor: "#e4ce7f",
            url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
            attribution:
                '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
            marker: [
                latLng(21.030653, 105.847130),
                latLng(21.567156, 105.825204)
            ]
        };
    },
    computed: {
        options() {
            return {
                onEachFeature: this.onEachFeatureFunction
            };
        },
        styleFunction() {
            const fillColor = this.fillColor;
            return () => {
                return {
                    weight: 2,
                    color: "#00FFFF",
                    opacity: 1,
                    fillColor: fillColor,
                    fillOpacity: 0.5
                };
            };
        },

    },
    methods: {
        removeMarker(index) {
            this.marker.splice(index, 1);
        },
        addMarker(event) {
            this.marker.push(event.latlng);
        }
    },
    async created() {
        this.loading = true;
        const response = await fetch("https://data.opendevelopmentmekong.net/dataset/55bdad36-c476-4be9-a52d-aa839534200a/resource/b8f60493-7564-4707-aa72-a0172ba795d8/download/vn_iso_province.geojson");
        const data = await response.json();
        this.geojson = data;
        this.loading = false;
    },


};
</script>