<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { Loader } from '@googlemaps/js-api-loader';

const mapElement = ref<HTMLDivElement | null>(null);
const drawingManagerRef = ref<google.maps.drawing.DrawingManager | null>(null);
const shapes = ref<google.maps.drawing.Overlay[]>([]);


const loader = new Loader({
    apiKey: 'AIzaSyBA-bqET1TgG2ZeiNySI5v9mG-9gjdn6hA',
    version: 'weekly',
    libraries: ['drawing'],
});

const setDrawingMode = (mode: google.maps.drawing.OverlayType | null) => {
    if (drawingManagerRef.value) {
        drawingManagerRef.value.setDrawingMode(mode);
    }
};

const handleDraw = (type: string) => {
    try {
        const overlayType = mapOverlayType(type);
        setDrawingMode(overlayType);

        google.maps.event.addListener(drawingManagerRef.value, 'overlaycomplete', (event: { overlay: any; }) => {
            shapes.value.push(event.overlay); 
            drawingManagerRef.value?.setDrawingMode(null); 
        });
    } catch (error) {
        console.error('Erro ao definir o modo de desenho:', error);
    }
};

const mapOverlayType = (type: string): google.maps.drawing.OverlayType | null => {
    switch (type) {
        case 'POLYGON':
            return google.maps.drawing.OverlayType.POLYGON;
        case 'RECTANGLE':
            return google.maps.drawing.OverlayType.RECTANGLE;
        case 'CIRCLE':
            return google.maps.drawing.OverlayType.CIRCLE;
        case 'MARKER':
            return google.maps.drawing.OverlayType.MARKER;
        default:
            return null;
    }
};

const saveShapes = async () => {
    const shapesData = shapes.value.map(shape => {
        if (shape instanceof google.maps.Polygon) {
            return {
                type: 'POLYGON',
                paths: shape.getPath().getArray().map(latLng => ({ lat: latLng.lat(), lng: latLng.lng() })),
            };
        } else if (shape instanceof google.maps.Rectangle) {
            const bounds = shape.getBounds();
            return {
                type: 'RECTANGLE',
                bounds: {
                    north: bounds?.getNorthEast().lat(),
                    east: bounds?.getNorthEast().lng(),
                    south: bounds?.getSouthWest().lat(),
                    west: bounds?.getSouthWest().lng(),
                },
            };
        } else if (shape instanceof google.maps.Circle) {
            return {
                type: 'CIRCLE',
                center: {
                    lat: shape.getCenter()?.lat(),
                    lng: shape.getCenter()?.lng(),
                },
                radius: shape.getRadius(),
            };
        } else if (shape instanceof google.maps.Marker) {
            return {
                type: 'MARKER',
                position: {
                    lat: shape.getPosition()?.lat(),
                    lng: shape.getPosition()?.lng(),
                },
            };
        }
        return null;
    }).filter(Boolean);

    console.log(shapesData);
};

onMounted(async () => {
    try {
        await loader.load();
        if (mapElement.value && window.google) {
            const map = new google.maps.Map(mapElement.value, {
                center: { lat: 24.886, lng: -70.268 },
                zoom: 5,
                mapTypeId: 'satellite',
                disableDefaultUI: true,
            });

            const drawingManager = new google.maps.drawing.DrawingManager({
                drawingMode: null,
                drawingControl: false,
                polygonOptions: {
                    strokeColor: "#FF0000",
                    fillOpacity: 0.20,
                    fillColor: "#FF0000",
                    strokeWeight: 5,
                    clickable: false,
                    editable: true,
                    zIndex: 1,
                },
                rectangleOptions: {
                    strokeColor: "#FF0000",
                    strokeOpacity: 0.8,
                    strokeWeight: 2,
                    fillColor: "#FF0000",
                    fillOpacity: 0.20,
                    editable: true,
                    draggable: true,
                },
                circleOptions: {
                    strokeColor: "#FF0000",
                    strokeOpacity: 0.8,
                    strokeWeight: 2,
                    fillColor: "#FF0000",
                    fillOpacity: 0.20,
                    editable: true,
                    draggable: true,
                },
            });

            drawingManager.setMap(map);
            drawingManagerRef.value = drawingManager;
        }
    } catch (error) {
        console.error('Erro ao carregar o Google Maps:', error);
    }
});

defineExpose({
    handleDraw, setDrawingMode, saveShapes
});
</script>

<template>
    <div ref="mapElement" class="map"></div>

</template>

<style scoped>
.map {
    width: 80vw;
    
;
}
</style>
