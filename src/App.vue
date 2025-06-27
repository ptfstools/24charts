<template>
    <div class="font-body dark" :hidden="chartURL == ''">
        <div id="app">
            <nav class="bg-fox-2 text-fox-2 fixed left-0 top-0 z-30 flex h-12 w-full items-center border-b border-fox-500 px-4">
                <div class="flex-1 font-medium logo" @click="goHome()">24Charts</div>
                <div class="flex-1 justify-center hidden md:flex">{{ currentChart.airport }} - {{ currentChart.author }}</div>
            </nav>
            <main class="bg-fox-1 text-fox-2 min-h-screen w-full pt-12 h-screen overflow-hidden">
                <div class="fox-app-swipe-target flex h-full w-full items-stretch">
                    <div class="z-20 w-0 transition-all xl:w-96" style="transform: translateX(0px); transition-duration: 150ms;">
                        <div class="bg-fox-2 flex flex-col items-stretch gap-4 p-4 relative z-20 h-full w-screen transition-transform xs:w-96 -translate-x-full xl:translate-x-0">
                            <div class="flex items-center gap-2">
                                <h2 class="flex-grow font-semibold">Charts</h2>
                                <button @click="downloadCurrentChart" class="download-btn">Download Current Chart</button>
                            </div>
                            <div class="flex flex-col items-stretch">
                                <div class="transition-all h-0"></div>
                                <ol class="flex chart-app-tabs" data-intro="Use these buttons to switch between types of chart" data-step="3">
                                    <li class="flex-grow basis-0" @click="currentCategory = 'GEN'" :class="charts.filter(c => c.author == currentChart.author && c.airport == currentChart.airport && c.category == 'GEN').length == 0 ? 'opacity-50 nocurs' : ''">
                                        <a :class="currentCategory == 'GEN' ? 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-red bg-fox-red text-fox-1 cursor-pointer dark:text-inherit' : 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-red cursor-pointer hover:brightness-110'">
                                            <span class="uppercase transition-all">GEN</span>
                                        </a>
                                    </li>
                                    <li class="flex-grow basis-0" @click="currentCategory = 'GND'" :class="charts.filter(c => c.author == currentChart.author && c.airport == currentChart.airport && c.category == 'GND').length == 0 ? 'opacity-50 nocurs' : ''">
                                        <a :class="currentCategory == 'GND' ? 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-pink bg-fox-pink text-fox-1 cursor-pointer dark:text-inherit' : 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-pink cursor-pointer hover:brightness-110'">
                                            <span class="uppercase transition-all">GND</span>
                                        </a>
                                    </li>
                                    <li class="flex-grow basis-0" @click="currentCategory = 'SID'" :class="charts.filter(c => c.author == currentChart.author && c.airport == currentChart.airport && c.category == 'SID').length == 0 ? 'opacity-50 nocurs' : ''">
                                        <a :class="currentCategory == 'SID' ? 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-purple bg-fox-purple text-fox-1 cursor-pointer dark:text-inherit' : 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-purple cursor-pointer hover:brightness-110'">
                                            <span class="uppercase transition-all">SID</span>
                                        </a>
                                    </li>
                                    <li class="flex-grow basis-0" @click="currentCategory = 'STAR'" :class="charts.filter(c => c.author == currentChart.author && c.airport == currentChart.airport && c.category == 'STAR').length == 0 ? 'opacity-50 nocurs' : ''">
                                        <a :class="currentCategory == 'STAR' ? 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-cyan bg-fox-cyan text-fox-1 cursor-pointer dark:text-inherit' : 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-cyan cursor-pointer hover:brightness-110'">
                                            <span class="uppercase transition-all">STAR</span>
                                        </a>
                                    </li>
                                    <li class="flex-grow basis-0" @click="currentCategory = 'APP'" :class="charts.filter(c => c.author == currentChart.author && c.airport == currentChart.airport && c.category == 'APP').length == 0 ? 'opacity-50 nocurs' : ''">
                                        <a :class="currentCategory == 'APP' ? 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-emerald bg-fox-emerald text-fox-1 cursor-pointer dark:text-inherit' : 'flex items-center justify-center border-b-4 py-2 transition-[background-color] border-fox-emerald cursor-pointer hover:brightness-110'">
                                            <span class="uppercase transition-all">APP</span>
                                        </a>
                                    </li>
                                </ol>
                            </div>
                            <div class="fox-scrollbars overflow-y-auto">
                                <ul class="mb-3 flex flex-col gap-3">
                                    <li v-for="i in getCategoryItems()">
                                        <a :class="getItemClass(i) + ' flex items-center gap-2 p-2 cursor-pointer hover:brightness-110'" @click="setChart(i)">
                                            <div class="flex flex-grow flex-col gap-1">
                                                <h4 class="font-medium leading-tight">{{ i.name.toLocaleUpperCase() }}</h4>
                                            </div>
                                        </a>
                                    </li>
                                </ul>
                                <div v-for="g in getRunwayGroupItems()">
                                    <div v-if="g.charts.filter(e => e.category == currentCategory).length > 0">
                                        <h3 class="mb-1 font-semibold">{{ g.runway }}</h3>
                                        <ul class="mb-3 flex flex-col gap-3">
                                            <li v-for="i in g.charts.filter(e => e.category == currentCategory)">
                                                <a :class="getItemClass(i) + ' flex items-center gap-2 p-2 cursor-pointer hover:brightness-110'" @click="setChart(i)">
                                                    <div class="flex flex-grow flex-col gap-1">
                                                        <h4 class="font-medium leading-tight">{{ i.name.toLocaleUpperCase() }}</h4>
                                                    </div>
                                                </a>
                                            </li>
                                        </ul>
                                    </div>
                                </div>
                            </div>
                            <p class="text-center text-sm italic transition-opacity hidden opacity-0"> No results. </p>
                        </div>
                    </div>
                    <div class="relative flex-grow">
                        <div class="bg-fox-300 dark:bg-fox-850 fox-app-swipe-target h-full w-full">
                            <div class="fox-app-swipe-target relative h-full overflow-hidden outline-none">
                                <div class="relative h-full overflow-hidden outline-none">
                                    <div class="fox-app-swipe-target h-full focus:outline-none" tabindex="0" id="ligmatizer"></div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </main>
        </div>
    </div>
    <div class="font-body dark airport-wrap">
        <div class="header">
            <h1 class="header-title">Select an Airport</h1>
            <input type="text" v-model="searchQuery" placeholder="Search airports (e.g., KJFK, EGLL)" class="search-input" />
        </div>
        <div v-if="filteredAirports.length === 0" class="no-results">
            <p>No airports found matching your search.</p>
        </div>
        <div v-else class="airport-grid-wrapper">
            <div class="airport-grid">
                <div v-for="i in filteredAirports" class="airport-card">
                    <h2 class="airport-code">{{ i.code }}</h2>
                    <div class="pack-list">
                        <div v-for="c in getPacks(i.code)" class="pack-item" @click="setChart(c)">
                            <span>{{ c.author }}</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import "@/assets/app.css";
import L from "leaflet";
import { generateAirports } from "ptfst-db";
import { onBeforeUnmount, onMounted, ref, watch, type Ref, computed } from "vue";
import { charts, type Chart } from "./Charts";

let currentChart: Ref<Chart> = ref(charts[0]);
let chartURL: Ref<string | null> = ref(null);
let currentCategory = ref("");
let searchQuery = ref("");

onMounted(() => {
    fresh();
    window.addEventListener("resize", refresh);
    window.addEventListener("popstate", fresh);
});

function fresh() {
    chartURL.value = window.location.hash;
    if (chartURL.value.length > 0) {
        chartURL.value = chartURL.value.replace("#", "");
        let c = charts.find(c => `${c.id}` == chartURL.value);
        if (c == undefined) {
            if (!window.location.href.endsWith("/24charts")) {
                window.location.href = "/";
            }
        } else {
            currentChart.value = c;
            currentCategory.value = c.category;
        }
    }
}

onBeforeUnmount(() => {
    window.removeEventListener("resize", refresh);
});

let map: L.Map;

function refresh() {
    if (map) {
        map.remove();
    }
    map = L.map('ligmatizer', {
        crs: L.CRS.Simple,
    }).setView([0, 0], 1);

    const img = new Image();
    img.src = "https://charts.awdevsoftware.org/" + currentChart.value.file;
    img.addEventListener("load", () => {
        let scaleFactor = img.naturalHeight / 400;
        L.imageOverlay(img.src, [[0, 0], [img.naturalHeight / scaleFactor, img.naturalWidth / scaleFactor]], {
            zIndex: 6914124124124
        }).addTo(map);
        map.panTo([img.naturalHeight / (scaleFactor * 2), img.naturalWidth / (scaleFactor * 2)], {
            animate: false
        });
    });
    getRunwayGroupItems();
}

function getCategoryItems() {
    return charts.filter(e => {
        return e.airport == currentChart.value.airport && e.category == currentCategory.value && e.author == currentChart.value.author && e.runways.length == 0;
    });
}

interface RunwayGroup {
    runway: string,
    charts: Chart[]
}

function getRunwayGroupItems(): RunwayGroup[] {
    let groups: RunwayGroup[] = [];
    let relevantCharts = charts.filter(e => {
        return e.airport == currentChart.value.airport && e.author == currentChart.value.author;
    });
    relevantCharts.forEach(c => {
        c.runways.forEach(r => {
            let g = groups.find(i => i.runway == r);
            if (g == undefined) {
                groups.push({
                    runway: r,
                    charts: []
                });
            }
            g = groups.find(i => i.runway == r);
            g?.charts.push(c);
        });
    });
    return groups;
}

function getItemClass(c: Chart) {
    if (currentChart.value.id != c.id) {
        return "bg-fox-3";
    }
    switch (c.category) {
        case "GEN":
            return "bg-fox-red";
        case "GND":
            return "bg-fox-pink";
        case "SID":
            return "bg-fox-purple";
        case "STAR":
            return "bg-fox-cyan";
        case "APP":
            return "bg-fox-emerald";
    }
}

function setChart(c: Chart) {
    window.location.hash = `${c.id}`;
    chartURL.value = `${c.id}`;
    currentChart.value = c;
    currentCategory.value = c.category;
    window.requestAnimationFrame(refresh);
}

function goHome() {
    window.location.hash = ``;
    chartURL.value = ``;
    currentChart.value = charts[0];
    currentCategory.value = "";
}

function getPacks(airport: string) {
    let packs: Chart[] = [];
    charts.filter(e => e.airport == airport).forEach(c => {
        let p = packs.find(e => e.author == c.author);
        if (p == undefined) {
            packs.push(c);
        }
    });
    return packs;
}

const filteredAirports = computed(() => {
    const query = searchQuery.value.toUpperCase();
    if (!query) return generateAirports();
    return generateAirports().filter(airport => airport.code.includes(query));
});

watch(currentChart, refresh);

function downloadChart(chart: Chart) {
    const url = `/24charts${chart.file}`;
    const link = document.createElement('a');
    link.href = url;
    link.setAttribute('download', chart.name);
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
}

function downloadCurrentChart() {
    if (currentChart.value) {
        downloadChart(currentChart.value);
    }
}
</script>