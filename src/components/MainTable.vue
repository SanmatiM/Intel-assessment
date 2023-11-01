<template>
    <div>
        <table>
            <thead>
                <tr>
                    <td :colspan="12">Dashboard SLA</td>
                </tr>
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
                <template v-for="(data, status, index) in productDataBystatus.data">
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
    </div>
</template>
  
<script>
export default {
    props: {
        productDataBystatus: Object,
    },
    computed: {
        wwData() {
            return `${this.getWWFromDate().year}WW${this.getWWFromDate().workweek}.${this.getWWFromDate().numofday}`;
        },
    },
    methods: {
        calstatusRowspan(data) {
            let sum = Object.keys(data).length + 1;
            for (const cores in data) {
                sum += Object.keys(data[cores]).length;
            }
            return sum;
        },
        getWWFromDate(date = null) {
            let currentDate = date || new Date();
            let startDate = new Date(currentDate.getFullYear(), 0, 1);
            let days = Math.floor((currentDate - startDate) / (24 * 60 * 60 * 1000));

            return {
                year: currentDate.getFullYear(),
                workweek: Math.ceil(days / 7),
                numofday: currentDate.getDay(),
            };
        },
        getStatusClass(status) {
            const statusClassMap = {
                Announced: 'status-announced',
                Discontinued: 'status-discontinued',
                Launched: 'status-launched',
                'Launched (with IPU)': 'status-launched-with-ipu',
            };
            return statusClassMap[status];
        }
    }
};
</script>
<style scoped>
.fas.fa-times {
    display: none;
}

.fas.fa-times.comment {
    display: block;
}

.overWrittenCells:hover .fas {
    display: block;
}

.innerCells {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
}

.innerCells.comment {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    gap: 15px;
}

table {
    width: 100%;
    white-space: nowrap !important;
}

table td {
    position: relative;
}

i {
    cursor: pointer;
}

.legendColorBox {
    margin: 0.4%;
    float: left;
    height: 20px;
    width: 30px;
    border: 1px solid grey;
    margin-right: 4%;
}

.inputBox {
    position: absolute;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
    text-align: center;
    border: 0;
    text-transform: uppercase !important;
}

.inputBoxOverWritten {
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
    text-align: center;
    border: 0;
    width: 80px;
    text-transform: uppercase !important;
    background: none !important;
}

.overWrittenCells {
    border: 2px solid rgb(194, 1, 1);
}

.overWrittenCells input {
    outline: 0;
}

input::placeholder {
    color: black;
}

input:focus::-webkit-input-placeholder {
    color: grey;
}

input[disabled] {
    cursor: text;
    background-color: inherit;
    color: black;
}

.legend-labels {
    white-space: nowrap !important;
    padding: 0%;
    display: flex;
    list-style-type: none;
    margin-bottom: 5px;
}

.legend-labels li {
    font-size: small;
    margin-right: 2%;
}

select {
    position: absolute;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
    text-align: center;
    border: 0;
}

table tr td:not(.skip),
table tr th {
    text-align: center;
}

td,
th {
    padding: 2px !important;
    width: 100px;
    border: 1px solid black;
}

.status-announced {
    background-color: #db7d7d;
    /* Red for Announced */
}

.status-discontinued {
    background-color: #e9ee6f;
    /* Dark Red for Discontinued */
}

.status-launched {
    background-color: #8cf38c;
    /* Green for Launched */
}

.status-launched-with-ipu {
    background-color: #008000;
    /* Dark Green for Launched (with IPU) */
}

/* .productColumn {
    width: 1%;
    background-color: white;
} */

.width1 {
    width: 1%;
    /* white-space: nowrap !important; */
}
</style>