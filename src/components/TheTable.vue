<template>
    <div>
        <HideBar :productDataBystatus="productDataBystatus" v-bind:hidestatus="hidestatus" v-on:update:hidestatus="hidestatus = $event" />
        <MainTable :productDataBystatus="productDataBystatus" />
    </div>
</template>
  
<script>
import { ref, computed } from 'vue';
import HideBar from './HideBar.vue'
import MainTable from './MainTable.vue';
import data from './../assets/data.json';

export default {
    components: {
        HideBar,
        MainTable,
    },
    setup() {
        const UIData = ref(data);
        const hidestatus = ref([]);

        const productDataBystatus = computed(() => {
            let tmp = {};
            let data = UIData.value;
            let statusSet = new Set();
            console.log(typeof data);
            data.forEach((element) => {
                let status = element.Status;
                let cores = element.Cores;

                // push status to set
                statusSet.add(status);

                if (hidestatus.value.includes(status)) return; // Hide by status
                if (!tmp[status]) tmp[status] = {};
                if (!tmp[status][cores]) tmp[status][cores] = [];

                tmp[status][cores].push(element);
            });

            // sort status in order
            const strings = new Set(statusSet);
            const sortedStringsArray = [...strings].sort();
            statusSet = new Set(sortedStringsArray);

            return {
                status: [...statusSet],
                data: tmp,
            };
        });

        function toggleAllStatus(checked) {
            if (checked) {
                hidestatus.value = productDataBystatus.value.status;
            } else {
                hidestatus.value = [];
            }
        }

        return {
            UIData,
            hidestatus,
            productDataBystatus,
            toggleAllStatus,
        };
    },
};
</script>
  