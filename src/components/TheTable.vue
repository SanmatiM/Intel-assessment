<template>
    <div>
        <!-- Hide By status Bar -->
        <nav class="navbar navbar-expand-lg navbar-light bg-light">
            <div class="container">
                <a class="navbar-brand mx-auto">Intel Dashboard SLA</a>
                <!-- Collapsible content -->
                <div class="collapse navbar-collapse" id="navbarNav">
                    <div class="row">
                        <!-- HideBarVue Component -->
                        <div class="col-md-8">
                            <HideBarVue :productDataBystatus="paginationMeta.statuses" v-bind:hidestatus="hidestatus"
                                v-on:update:hidestatus="hidestatus = $event" />
                        </div>

                        <!-- Search Input Form -->
                        <div class="col-md-4">
                            <form class="form-inline ml-auto">
                                <input type="search" placeholder="Search" aria-label="Search" v-model="searchKeywords"
                                    class="form-control mr-sm-2" />
                            </form>
                        </div>

                        
                    </div>
                </div>
            </div>
        </nav>
       
        <!-- Main Table Design -->
        <table>
            <thead>

                <tr>
                    <th colspan="3">{{ wwData }}</th>
                    <th colspan="8">Product Info</th>
                </tr>
                <tr>
                    <th>Status</th>
                    <th>Cores</th>
                    <th class="width1">Product</th>
                    <th class="width1">Lithography</th>
                    <th>Threads</th>
                    <th>Base Freq</th>
                    <th>Max Turbo Freq</th>
                </tr>
            </thead>
            <tbody>


                <template v-for="(data, status, index) in paginationMeta.data">
                    <!-- status -->
                    <tr :class="getStatusClass(status)">
                        <td class="width1" :rowspan="calstatusRowspan(data)">
                            {{ status }}
                        </td>
                    </tr>

                    <template v-for="cores in Object.keys(data)">
                        <!-- cores -->
                        <tr :class="getStatusClass(status)">
                            <td class="width1" :rowspan="Object.keys(data[cores]).length + 1">
                                {{ cores }}
                            </td>
                        </tr>

                        <tr v-for="(v, k) in data[cores]" :class="getStatusClass(v.Status)">
                            <!-- product -->
                            <td class="productColumn">{{ v.Product }}</td>

                            <!-- Lithography -->
                            <td>{{ v.Lithography }}</td>

                            <!-- Threads -->
                            <td>
                                <div class="innerCells">
                                    <input :value="v.Threads" :disabled="true" type="text" />
                                </div>
                            </td>

                            <!-- Base Freq -->
                            <td>
                                <div class="innerCells">
                                    <input :value="v.Base_Freq" :disabled="true" type="text" />
                                </div>
                            </td>

                            <!-- Max Turbo Freq -->
                            <td>
                                <div class="innerCells">
                                    <input :value="v.Max_Turbo_Freq" type="text" :disabled="true" />
                                </div>
                            </td>
                        </tr>
                    </template>
                </template>
            </tbody>

        </table>
        <div class="row">
            <div class="col-12">
                <Pagination :activePage="currentPage" :data="paginationMeta.pages" :callback="setCurrentPage" />
            </div>
        </div>
        <!-- End of Table Design -->
    </div>
</template>
  
  
<script>
import { ref, computed } from "vue";

import data from "../assets/data.json";
import HideBarVue from "./HideBar.vue";
import Pagination from "./Pagination.vue";
export default {
    components: {
        HideBarVue,
        Pagination
    },
    methods: {
        getStatusClass(status) {
            const statusClassMap = {
                Announced: 'status-announced',
                Discontinued: 'status-discontinued',
                Launched: 'status-launched',
                'Launched (with IPU)': 'status-launched-with-ipu',
            };
            return statusClassMap[status];
        }
    },
    setup() {

        const now = new Date();

        let dataRef = data
        let UIData = data;
        let allCheck = false;
        let wwInfo = {
            year: now.getFullYear(),
            workweek: Math.ceil(Math.floor((now - new Date(now.getFullYear(), 0, 1)) / (24 * 60 * 60 * 1000)) / 7),
            numofday: now.getDay(),
        };

        /***** Reactive properties*****/
        let perPage = ref(50)
        let currentPage = ref(1)
        let hidestatus = ref([]);
        let allCheckBox = ref([]);
        const searchKeywords = ref("")

        /***** COMPUTED PROPERTIES *****/
        const wwData = computed(() => `${wwInfo.year}WW${wwInfo.workweek}.${wwInfo.numofday}`);

        let paginationMeta = computed(() => {

            let stats = {};
            let tmp = {};
            let data_list = [];
            let data = UIData;
            let statusSet = new Set();

            /*** Take statuses & search by input */
            data.forEach((element) => {

                let status = element.Status;
                // push status to set
                statusSet.add(status);

                if (hidestatus.value.includes(status)) return; // Hide by status

                if (searchKeywords !== "") {

                    /***** Filterable columns ****/
                    let searchableColumns = [
                        "Product",
                        "Status",
                        "Cores",
                        "Threads",
                        "Max_Turbo_Freq",
                        "Base_Freq",
                        "Cache(MB)"
                    ];

                    if (searchableColumns.some((key) => { const value = element[key] ? element[key].toString().toLowerCase() : ''; return value.toString().toLowerCase().includes(String(searchKeywords.value).toLowerCase()); })) {

                        data_list.push(element);

                        if (stats[element["Status"]]) { stats[element["Status"]]++; }
                        else { stats[element["Status"]] = 1; }
                    }
                } else {

                    data_list.push(element);
                    if (stats[element["Status"]]) { stats[element["Status"]]++; }
                    else { stats[element["Status"]] = 1; }
                }
            });

            //currentPage.value = 1;
            data_list.slice(currentPage.value * perPage.value - perPage.value, currentPage.value * perPage.value).forEach((element) => {
                let status = element.Status;
                let cores = element.Cores;

                if (!tmp[status]) tmp[status] = {};
                if (!tmp[status][cores]) tmp[status][cores] = [];

                tmp[status][cores].push(element);
            });

            // sort status in order
            const strings = new Set(statusSet);
            const sortedStringsArray = [...strings].sort();
            statusSet = new Set(sortedStringsArray);

            return {
                totalPages: Math.ceil(data_list.length / perPage.value),
                data: tmp,
                pages: Array.from({ length: Math.ceil(data_list.length / perPage.value) }, (_, i) => i + 1),
                statuses: [...statusSet],
                stats: Object.entries(stats).map(([name, y]) => ({ name, y }))
            }
        });

        /***** METHODS *****/
        const setCurrentPage = page => currentPage.value = page;

        const hideShowALLstatus = () => {

            if (!document.querySelector(".styled").checked) {

                hidestatus.value = [];
                allCheckBox.value = [];
            } else {

                hidestatus.value = paginationMeta.value.statuses;
                allCheckBox.value = paginationMeta.value.statuses;
            }

            allCheck = !allCheck.value;
            if (!allCheck) {

                hidestatus.value = [];
                allCheckBox.value = [];
            }
        };

        const calstatusRowspan = (data) => {

            let sum = Object.keys(data).length + 1;
            for (const cores in data) {
                sum += Object.keys(data[cores]).length;
            }

            return sum;
        };

        return {
            hidestatus, allCheckBox, UIData,
            wwInfo, allCheck, dataRef, perPage,
            wwData, hideShowALLstatus, paginationMeta,
            setCurrentPage, calstatusRowspan, currentPage,
            searchKeywords
        }
    },
};
</script>
  
  
<style scoped>
    @import './styles.css';
</style>