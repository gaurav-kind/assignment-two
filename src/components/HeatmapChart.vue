<script setup>
import { onMounted, ref,defineEmits } from 'vue';

const emit = defineEmits(['onselect-date'])

const allDateChanges = ref(null);

const cells = Array.from(new Array(365)).map((_, index) => {
    const curr_Date = new Date()
    const current_date = curr_Date.setDate(curr_Date.getDate() - index)
    const previous_date = curr_Date.setDate(new Date(current_date).getDate() - 1)
    if (new Date(current_date).getMonth() != new Date(previous_date).getMonth()) {
        return [new Date(current_date).toISOString().split("T")[0], ...Array.from({ length: 7 }, () => ["space"])]
    }
    // console.log(new Date(current_date).toISOString().split("T")[0], "----");

    return new Date(current_date).toISOString().split("T")[0]
}).flat().reverse()

const startMonth = (value) => {
    if (value == "space") {
        return false
    }
    const day = new Date(value)
    if (day.getDate() == 1) {
        const shortMonth = day.toLocaleString('default', { month: 'short' });
        return shortMonth
    }
    return false
}
const getCellData = (value) => {
    const date = new Date(value);
    let currentDataChanges = 0
    if (allDateChanges?.value?.data && allDateChanges.value.data[value]) {
        currentDataChanges = allDateChanges.value.data[value]
    }
    const day = date.getDate();
    const monthName = date.toLocaleString('default', { month: 'long' });
    const year = date.getFullYear();
    return `${currentDataChanges} changes on ${monthName} ${day} ,${year}`
}

const fetchAllChanges = async () => {
    try {
        const response = await fetch("https://heatmapchanges.free.beeceptor.com/changes/all");
        allDateChanges.value = await response.json();
        console.log(allDateChanges.value.data);

    } catch (error) {
        console.error("Error fetching products:", error);
    }
};

onMounted(fetchAllChanges);

const getBackgroundColor = (value) => {
   let currentDataChanges = 0
    if (allDateChanges.value && allDateChanges?.value?.data && allDateChanges?.value.data[value]) {
        currentDataChanges = allDateChanges.value.data[value]
    }
  if (currentDataChanges == 0  ) return "rgb(230, 228, 228)";
  else if (currentDataChanges <= 1) return "rgb(227, 179, 184)";
  else if (currentDataChanges <= 2) return "rgb(242, 132, 153)";
  else if (currentDataChanges <= 4) return "rgb(240, 83, 112)";
  else if (currentDataChanges <= 6) return "rgba(244, 12, 34, 0.97)";
};

const updateSelectedDate = (date) => {
    emit("onselect-date", date)
}

</script>

<template>
    <div class="heatmap-card">
        <h2>Change Heat Map</h2>
        <div class="heatmap-container">
            <div class="week-names">
                <div class="title">Sun</div>
                <div class="title">Tue</div>
                <div class="title">Thur</div>
                <div class="title">Sat</div>
            </div>
            <div class="container">
                <div v-for="cell, index in cells" :key="index">
                    <div v-if="startMonth(cell)" class="monthName title">{{ startMonth(cell) }}</div>
                    <div v-if="cell == 'space'">
                        <div class="empty-cell"></div>
                    </div>
                    <div v-else :title="getCellData(cell)" :style="{ backgroundColor: getBackgroundColor(cell) }" class="cell" @click="updateSelectedDate(cell)"></div>
                </div>
            </div>

        </div>
        <div class="range">
            <div>less</div>
            <div class="range-color">
                <div class="cell"></div>
                <div style="background: rgb(227, 179, 184)" class="cell"></div>
                <div style="background: rgb(242, 132, 153)" class="cell"></div>
                <div style="background: rgb(240, 83, 112)" class="cell"></div>
                <div style="background: rgba(244, 12, 34, 0.97)" class="cell"></div>
            </div>

            <div>more</div>
        </div>
    </div>
</template>

<style scoped>
.heatmap-card {
    width: auto;
    padding: 30px;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    overflow-x: auto;
}

.heatmap-container {
    display: flex;
    margin-top: 30px;
}

.title {
    font-size: small;
    font-weight: 600;
}

.week-names {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
}

.container {
    position: relative;
    display: grid;
    grid-template-rows: repeat(7, auto);
    gap: 2px;
    grid-auto-flow: column;
}

.monthName {
    position: absolute;
    top: -20px;
}

.cell {
    height: 20px;
    width: 20px;
    background: rgb(230, 228, 228);
    cursor: pointer;
}

.empty-cell {
    height: 20px;
    width: 20px;
}

.range {
    margin-top: 5%;
    display: flex;
    gap: 15px;
    justify-content: end;
}

.range-color {
    display: flex;
    gap: 2px;
}
</style>