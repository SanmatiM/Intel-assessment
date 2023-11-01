<template>
    <div class="hideBar">
        <!-- All statuses -->
        <label class="hideLabel"> Hide: </label>
        <div class="checkbox">

            <input :id="productDataBystatus.status" type="checkbox" class="styled" :value="productDataBystatus.status"
                @click="hideShowALLstatus" :checked="hidestatus.includes(productDataBystatus.status)" />
            <label :for="productDataBystatus.status">All statuses</label>

            <!-- Dynamic status -->
            <div v-for="status in productDataBystatus.status" :key="status">
                <input :id="status" type="checkbox" class="styled" :value="status" @click="toggleStatus(status)"
                    :checked="hidestatus.includes(status)" />
                <label :for="status">{{ status }}</label>
            </div>
        </div>

    </div>
</template>
  
<script>
export default {
    props: {
        productDataBystatus: Object,
        hidestatus: Array,
    },
    methods: {
        hideShowALLstatus() {
            if (this.hidestatus.length === this.productDataBystatus.status.length) {
                // All statuses are currently hidden, so unhide them all
                this.$emit('update:hidestatus', []);
            } else {
                // Not all statuses are hidden, so hide them all
                this.$emit('update:hidestatus', this.productDataBystatus.status.slice());
            }
        },
        toggleStatus(status) {
            const updatedHidestatus = [...this.hidestatus];
            const statusIndex = updatedHidestatus.indexOf(status);
            if (statusIndex !== -1) {
                // Status is in hidestatus, so remove it (unhide)
                updatedHidestatus.splice(statusIndex, 1);
            } else {
                // Status is not in hidestatus, so add it (hide)
                updatedHidestatus.push(status);
            }
            this.$emit('update:hidestatus', updatedHidestatus);
        },
    },
};
</script>
<style scoped>
.hideBar {
    list-style: none;
    display: flex;
}

.checkbox {
    list-style: none;
    display: flex;
}

.checkbox label {
    margin-left: 10px;
}

.redActual {
    width: 1%;
    color: red;
    background-color: #dcdcdc;
}

</style>