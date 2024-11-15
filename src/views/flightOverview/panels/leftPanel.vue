<template>
    <div style="position: relative;">
        <flightOverview class="flightOverview" />
    </div>
</template>
<script lang="ts" setup>
import flightOverview from "/src/components/flightOverview.vue";



</script>
<style scoped lang="less">
.OverviewTrendreason {
    width: 360px;
    height: 228px;
    background: url("/src/assets/echarts/feiJiHangBanBJ.png") no-repeat;
    background-size: 100% 100%;
    opacity: 0.7;
}

.Overview {
    position: absolute;
    top: 0px;
    left: 2%;
    display: flex;

    .OverviewData {
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: space-around;

        span {
            width: 90px;
            height: 24px;
            font-size: 16px;
            color: #ffffff;
            line-height: 24px;
            text-align: center;
        }
    }

    div:nth-child(2) {
        width: 100%;
        display: flex;
        margin-top: 20px;
        margin-left: 15%;
        flex-direction: column;

        span {
            margin-top: 10px;
            margin-left: 20px;
            font-size: 16px;
            color: #ffffff;
        }
    }

}

.Trend {
    position: absolute;
    top: 268px;
    left: 2%;
    height: 286px;
}

.title {
    height: 30px;
    font-size: 20px;
    color: #ffffff;
    line-height: 30px;
    padding-left: 35px;
    text-align: left;
}

.reason {
    position: absolute;
    top: 574px;
    left: 2%;
    height: 372px;
}

.basePiechart {
    position: absolute;
    left: 10px;
    top: 50px;
    right: 10px;
    bottom: 10px;
    background: url('/src/assets/echarts/feiji.png') no-repeat center center;
}
</style>