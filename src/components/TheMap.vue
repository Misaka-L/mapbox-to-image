<script setup lang="ts">
import mapboxgl from "mapbox-gl"
import { onMounted, onUnmounted, reactive, ref } from 'vue'
import { saveAs } from 'file-saver';
import PreviewDialog from '@/components/PreviewDialog.vue'

const mapContainer = ref<null | HTMLDivElement>(null)
const tempMapContainer = ref<null | HTMLDivElement>(null)
const map = ref<null | mapboxgl.Map>(null)

const mapOptions = reactive({
    accessToken: 'pk.eyJ1IjoibWlzYWthbCIsImEiOiJjazduNmx4aDEwM2g4M2xzd2x1MDMzYnF2In0.ZA-MP7-oAwO_ZdufRuuUZw',
    style: 'mapbox://styles/misakal/cls3eit5o001i01r0c81kaeim'
})

const exportOptions = reactive({
    height: 1440,
    width: 2560,
    dpi: 96
})

onMounted(() => {
    initMap()
})

onUnmounted(() => {
    map.value?.remove()
    map.value = null
})

function initMap() {
    map.value?.remove()
    map.value = null

    map.value = new mapboxgl.Map({
        container: mapContainer.value as HTMLElement,
        style: mapOptions.style,
        accessToken: mapOptions.accessToken
    })
}

function renderMap(preview: boolean) {
    const actualPixelRatio = window.devicePixelRatio;
    Object.defineProperty(window, 'devicePixelRatio', {
        get() { return exportOptions.dpi / 96; },
    });

    const tempMap = new mapboxgl.Map({
        container: tempMapContainer.value as HTMLElement,
        style: mapOptions.style,
        accessToken: mapOptions.accessToken,
        attributionControl: false,
        zoom: map.value?.getZoom(),
        bearing: map.value?.getBearing(),
        center: map.value?.getCenter(),
        pitch: map.value?.getPitch(),
        interactive: false,
        preserveDrawingBuffer: true,
        fadeDuration: 0,
        // transformRequest: (map as any)._requestManager._transformRequestFn
    })

    tempMap.once('idle', () => {
        const mapCanvas = tempMap.getCanvas()

        if (preview) {
            showPreview(mapCanvas.toDataURL())
        } else {
            mapCanvas.toBlob((blob) => {
                saveAs(blob as Blob, 'map.png')
            })
        }

        tempMap.remove()

        Object.defineProperty(window, 'devicePixelRatio', {
            get() { return actualPixelRatio; },
        });
    })
}

const showPreviewDialog = ref(false)
const previewUrl = ref('')

function showPreview(url: string) {
    showPreviewDialog.value = true
    previewUrl.value = url
}
</script>

<template>
    <PreviewDialog v-model="showPreviewDialog">
        <img :src="previewUrl" />
    </PreviewDialog>
    <div class="the-map">
        <h2>Map options</h2>
        <div class="map-options-bar">
            <input class="map-options-item" v-model="mapOptions.accessToken" type="text" placeholder="Access token" />
            <input class="map-options-item" v-model="mapOptions.style" type="text" placeholder="Map style" />
            <button @click="initMap()">Apply</button>
        </div>
        <h2>Export options</h2>
        <div class="map-options-bar">
            <input class="map-options-item" v-model="exportOptions.height" type="number" placeholder="Height (px)" />
            <input class="map-options-item" v-model="exportOptions.width" type="number" placeholder="Width (px)" />
            <input class="map-options-item" v-model="exportOptions.dpi" type="number" placeholder="DPI" />
        </div>
        <div class="map-options-bar">
            <button class="map-options-item" @click="renderMap(false)">Export</button>
            <button class="map-options-item" @click="renderMap(true)">Preview</button>
        </div>
        <div class="map-container" ref="mapContainer"></div>
    </div>
    <Teleport to="body">
        <div class="the-render-map">
            <div ref="tempMapContainer" :style="{ height: exportOptions.height + 'px', width: exportOptions.width + 'px' }">
            </div>
        </div>
    </Teleport>
</template>

<style scoped>
.the-map {
    display: flex;
    flex-direction: column;
    height: 100%;

    .map-container {
        flex: 1;
    }

    .map-options-bar {
        display: flex;

        .map-options-item {
            flex: 1;
        }
    }
}

.the-render-map {
    overflow: hidden;
    height: 0;
    widows: 0;
    position: fixed;
}


img {
    height: 100%;
    width: 100%;
    object-fit: contain;
}
</style>