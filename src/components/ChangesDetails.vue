<script setup>
import { defineProps, ref, watchEffect } from "vue";

const allChangesData = ref(null);
const props = defineProps(['selectDate'])

const getselectedDateData = async () => {
    if (!props.selectDate) return
    try {
        const response = await fetch(`https://demoone.free.beeceptor.com/changes/${props.selectDate}`);
        allChangesData.value = await response.json();
        console.log(allChangesData.value);
    } catch (error) {
        console.error("Error fetching products:", error);
        allChangesData.value = null
    }
}
watchEffect(() => {
    getselectedDateData()
})

</script>

<template>
    <div class="changes-details">
        <h2 v-if="!props.selectDate">Select a box to view changes for that day</h2>
        <div v-else-if="allChangesData">
            <h2>Showing {{ allChangesData?.data?.length }} changes that occurred on {{ props.selectDate }}</h2>
            <div v-for="item in allChangesData?.data" :key="item">
                <div>{{ item }}</div>
            </div>
        </div>
        <h2 v-else>Showing 0 changes that occurred on {{ props.selectDate }}</h2>
    </div>
</template>

<style>
.changes-details {
    margin-top: 50px;
    margin-bottom: 50px;
    width: auto;
    padding: 30px;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    overflow-x: auto;
}
</style>