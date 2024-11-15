<template>
    <div class="cont">
        <div class="title">
            <div>Assignment Hyperhire</div>
        </div>
        <div class="navigation">
            <div 
                class="navigationItem" 
                :class="curSeleNavigation === index ? 'navigationItemSel' : ''" 
                v-for="(item, index) in navigation" 
                :key="index" 
                @click="selNavigation(index)"
            >
                {{ item.name }}
            </div>
        </div>
        <div class="notes">{{ notes }}</div>
        <div class="curTime">
            <div>
                <p>{{ date }}</p>
                <p>{{ week }}</p>
            </div>
            <div>{{ curTime }}</div>
        </div>
    </div>
</template>

<script lang="ts" setup>
import { reactive, ref, onMounted } from 'vue'
import dayjs from "dayjs";
import { useRouter } from 'vue-router'

const router = useRouter()

onMounted(() => {
    updateTime()
})

const navigation = reactive([
    { name: 'Globe Overview', path: '/' },
    { name: 'Airplane Overview', path: 'airlinerMaintain' },
])

const curSeleNavigation = ref(0)
const selNavigation = (index: number) => {
    router.push(navigation[index].path)
    curSeleNavigation.value = index;
}

const notes = ref("")

const formatDate = (dateVal) => {
    const arrDay = dayjs(dateVal).format('YYYY/MM/DD HH:mm:ss').split(' ')
    const weekDays = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
    return {
        date: arrDay[0].split('/').join('-'),
        time: arrDay[1],
        week: weekDays[dayjs(dateVal).day()],
    }
}

const date = ref('')
const week = ref('')
const curTime = ref('')
const updateTime = () => {
    setInterval(() => {
        const current = formatDate(dayjs())
        date.value = current.date
        week.value = current.week
        curTime.value = current.time
    }, 1000)
}
</script>

<style scoped lang="less">
.cont {
    height: 130px;
    background-color: #0a0c10;
    display: flex;
}

.title {
    margin-left: 1%;
    margin-top: 35px;
    font-size: 16px;
    color: #dedae4;
}

.navigation {
    margin-left: 2%;
    margin-top: 40px;
    width: 630px;
    height: 40px;
    font-size: 20px;
    color: #ffffff;
    display: flex;

    .navigationItem {
        flex: 1;
        margin-left: 20px;
        line-height: 42px;
        cursor: pointer;
    }

    .navigationItemSel {
        color: #ff7e36;
    }
}

.notes {
    margin-top: 42px;
    margin-left: 2%;
    height: 42px;
    line-height: 42px;
    font-size: 16px;
    color: #ffffff;
    text-align: left;
}

.curTime {
    margin-top: 40px;
    margin-left: 2%;
    width: 300px;
    display: flex;

    p {
        font-size: 14px;
        color: #ffffff;
    }

    div {
        font-size: 46px;
        color: #ffffff;
    }
}
</style>
