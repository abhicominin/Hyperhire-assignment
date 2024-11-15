<template>
    <div class="flightOverview">
        <leftPanel class="leftPanel"></leftPanel>
    </div>
</template>
<script lang="ts" setup>
import leftPanel from './panels/leftPanel.vue';


</script>
<style scoped lang="less">
.flightOverview {
    display: flex;
    height: 100vh;
    background-color: #11101e;
}

.leftPanel {
    flex: 4;
}

.middlePanel {
    flex: 1;
    margin-right: 3%;
}

// .rightPanel {
//     flex: 1;
// }
</style>