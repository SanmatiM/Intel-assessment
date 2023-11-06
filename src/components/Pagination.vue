<template>
    <nav aria-label="Pagination">
        <ul class="pagination justify-content-center">
            <li v-for="number in data" :class="'page-item'+(number === activePage ? ' active' : '')">
                <a role="button" class="page-link" @click="callback(number)">{{ number }}</a>
            </li>
        </ul>
    </nav>
</template>

<script>
    export default {
        name: "Pagination",
        props: {
            data: Array,
            callback: Function,
            activePage: Number
        },
        setup() {

            //
        }
    }
</script>
<style scoped>
/* Center the pagination */
/* Center the pagination */
.pagination {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

/* Style individual page links */
.page-link {
  display: inline;
  margin: 0 5px;
  padding: 5px 10px;
  text-decoration: none;
  cursor: pointer;
  background-color: transparent;
  border: none;
  color: #007BFF;
}

/* Active page link (current page) */
.page-item.active .page-link {
  font-weight: bold;
  color: #0056b3;
}

/* Hover effect on page links */
.page-link:hover {
  text-decoration: underline;
}

</style>
