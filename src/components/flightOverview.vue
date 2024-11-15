<template>
    <div id="flyOverview" :style="{ width: '100%', height: '100%' }"></div>
</template>

<script lang="ts" setup>
import { reactive, ref, onMounted, onBeforeUnmount } from 'vue';
import * as echarts from 'echarts/core';
import { use } from 'echarts/core';
import { GeoComponent } from 'echarts/components';
import { CanvasRenderer } from 'echarts/renderers';
import { LinesChart, EffectScatterChart } from 'echarts/charts';
import datajson from '../assets/json/world.json';

use([CanvasRenderer, GeoComponent, LinesChart, EffectScatterChart]);
echarts.registerMap('world', datajson);

onMounted(() => {
    init();
});

onBeforeUnmount(() => {
    window.onresize = null;
});

const flyOverview = reactive({
    backgroundColor: 'transparent',
    geo: {
        map: 'world',
        zoom: 1.3,
        label: {
            emphasis: {
                show: false,
            },
        },
        roam: true,
        itemStyle: {
            areaColor: '#16213e',
            borderColor: '#5b66a1',
            emphasis: {
                areaColor: '#2a333d',
                borderColor: 'blue', // Blue outline on region hover
                borderWidth: 2,
            },
        },
        regions: [
            {
                name: 'India',
                itemStyle: {
                    opacity: 0.4,
                    borderColor: '#ffd591',
                    borderWidth: 3,
                    areaColor: '#485377',
                },
            },
        ],
    },
});

const trackData = reactive([
    [
        { name: 'Delhi' }, 
        { name: 'Bangalore', icon: 'img_bangalore.png' },
    ],
    [
        { name: 'Bangalore' },
        { name: 'Delhi', icon: 'img_delhi.png' },
    ],
]);

const geoCoordMap = reactive({
    Delhi: [77.10002952485256, 28.558216772973513],
    Bangalore: [77.70688123260176, 13.199280935136354],
});

const convertData = (data) => {
    let res = [];
    for (let i = 0; i < data.length; i++) {
        let dataItem = data[i];
        let fromCoord = geoCoordMap[dataItem[0].name];
        let toCoord = geoCoordMap[dataItem[1].name];
        if (fromCoord && toCoord) {
            res.push({
                fromName: dataItem[0].name,
                toName: dataItem[1].name,
                coords: [fromCoord, toCoord],
            });
        }
    }
    return res;
};

const planePath = 'path://M1705.06,1318.313v-89.254l-319.9-221.799l0.073-208.063c0.521-84.662-26.629-121.796-63.961-121.491c-37.332-0.305-64.482,36.829-63.961,121.491l0.073,208.063l-319.9,221.799v89.254l330.343-157.288l12.238,241.308l-134.449,92.931l0.531,42.034l175.125-42.917l175.125,42.917l0.531-42.034l-134.449-92.931l12.238-241.308L1705.06,1318.313z';

const init = () => {
    let series = reactive([
        {
            name: 'track',
            type: 'lines',
            zlevel: 1,
            effect: {
                show: true,
                period: 6,
                trailLength: 0.7,
                color: '#fff',
                symbolSize: 3,
            },
            lineStyle: {
                color: '#a6c84c',
                width: 0,
                curveness: 0.2,
            },
            data: convertData(trackData),
        },
        {
            name: 'track',
            type: 'lines',
            zlevel: 2,
            effect: {
                show: true,
                period: 6,
                trailLength: 0,
                symbol: planePath,
                symbolSize: 15,
            },
            lineStyle: {
                color: '#a6c84c',
                width: 1,
                opacity: 0.4,
                curveness: 0.2,
            },
            data: convertData(trackData),
        },
        {
            name: 'track',
            type: 'effectScatter',
            coordinateSystem: 'geo',
            zlevel: 2,
            rippleEffect: {
                brushType: 'stroke',
            },
            label: {
                show: true,
                position: 'right',
                formatter: '{b}',
            },
            symbolSize: 15,
            itemStyle: {
                color: function (params) {
                    // Set color based on the location name
                    return params.data.name === 'Delhi' ? '#FD8A8A' : '#4CC9FE';
                },
            },
            emphasis: {
                itemStyle: {
                    borderColor: 'blue', // Blue outline on point hover
                    borderWidth: 7,
                },
            },
            data: trackData.map((dataItem) => {
                return {
                    name: dataItem[1].name,
                    value: geoCoordMap[dataItem[1].name].concat([dataItem[1].icon]),
                };
            }),
        },
    ]);

    flyOverview.series = series;
    let myChart = echarts.init(document.getElementById('flyOverview'));
    myChart.setOption(flyOverview);
    window.onresize = function () {
        myChart.resize();
    };
};
</script>

<style scoped lang="less">
#flyOverview {
    width: 1090px;
    height: 1000px;
}
</style>
