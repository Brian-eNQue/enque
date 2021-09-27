<template>
    <GmapMap ref="mapRef" :center="{lat:10, lng:10}" :zoom="7" map-type-id="terrain" style="width: 500px; height: 300px">
        <GmapMarker :key="index" v-for="(m, index) in markers" :position="m.position" :clickable="true"
            :draggable="true" @click="center=m.position" />
    </GmapMap>
</template>

<script>
    import { gmapApi } from 'gmap-vue';

    export default {
        computed: {
            google: gmapApi,
        },
        data() {
            return {
                center: {
                    lat: 10.0,
                    lng: 10.0
                },
                markers: [
                    { position: { lat: 10.0, lng: 10.0 } }, 
                    { position: { lat: 11.0, lng: 11.0 } }
                ]
            }
        },
        async mounted() {
            // At this point, the child GmapMap has been mounted, but
            // its map has not been initialized.
            // Therefore we need to write mapRef.$mapPromise.then(() => ...)
            let pos;

            this.$refs.mapRef.$mapPromise.then((map) => {
                if (navigator.geolocation) {
                    navigator.geolocation.getCurrentPosition((position) => {
                        pos = {
                            lat: position.coords.latitude,
                            lng: position.coords.longitude
                        };
                        map.setCenter(pos); 
                        this.markers.push({position: pos}); 
                    })
                }
            })
            await this.$gmapApiPromiseLazy();
            
        }
    };
</script>