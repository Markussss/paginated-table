<template>
    <div v-if="!loaded">
        <h2>
            Loading{{ loadingDots }}
        </h2>
    </div>
    <div v-else-if="items.length === 0 && loaded">
        Did not get any data
    </div>
    <div v-else-if="items.length > 0 && loaded" class="table-container">
        <table>
            <thead>
                <tr>
                    <th v-for="(column, field) in columns" :key="field">
                        {{ column }}
                    </th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in visibleItems" :key="item.name">
                    <td v-for="field in Object.keys(columns)" :key="item.name + field">
                        <slot :name="field" :item="item">
                            {{ item[field] }}
                        </slot>
                    </td>
                </tr>
            </tbody>
        </table>
        <div class="pagination">
            <button @click="selectedPage = (selectedPage - 1)" :disabled="selectedPage === 1">
                &lt;
            </button>
            <button v-for="page in pages"
                :key="page"
                @click="selectedPage = page"
                :class="{ selected: selectedPage === page }">
                    {{ page }}
            </button>
            <button @click="selectedPage = (selectedPage + 1)" :disabled="selectedPage === this.pages.length">
                &gt;
            </button>
        </div>
    </div>
</template>
<script>
export default {
    data () {
        return {
            items: [],
            selectedPage: 1,
            loadingDots: '',
            loadingDotsInterval: '',
            loaded: false
        }
    },
    name: 'PaginatedTable',
    props: {
        /**
         * The URL for the API to load in the table
         */
        apiUrl: {
            type: String,
            required: false,
            default: ''
        },
        /**
         * The definition of the columns that should be present in the table. Field names are the fields present in the
         * JSON-objects loaded from the API, the values of the fields should be the header names.
         */
        columns: {
            type: Object,
            required: false,
            default: () => {
                return {}
            },
        },
        /**
         * How many items should be shown per page in the table.
         */
        itemsPerPage: {
            type: Number,
            required: false,
            default: 20,
        },
        /**
         * A custom function to transform the response from the API to an array of objects.
         */
        responseFunction: {
            type: Function,
            required: false,
            default: () => {
                return response => response
            }
        }
    },
    async created() {
        this.loadingDotsInterval = setInterval(() => {
            if (this.loadingDots.length >= 3) {
                this.loadingDots = ''
            }
            this.loadingDots += '.'
        }, 500)

        this.items = await fetch(this.apiUrl)
            .then(response => response.json())
            .then(this.responseFunction)
            .then(result => {
                this.loaded = true
                clearInterval(this.loadingDotsInterval)
                return result
            })
    },
    computed: {
        visibleItems() {
            let startIndex = (this.selectedPage - 1) * this.itemsPerPage
            return this.items.slice(startIndex, startIndex + this.itemsPerPage)
        },
        numberOfPages() {
            return Math.ceil(this.items.length / this.itemsPerPage)
        },
        pages() {
            return [...Array(this.numberOfPages)].map((unused, i) => i + 1)
        }
    },
    methods: {
    }
}
</script>
<style scoped>
    .table-container {
        overflow-x: scroll;
    }
    table {
        text-align: left;
        padding: 5px;
        margin: 1em auto;
        border-collapse: collapse;
        width: 100%;
    }
    th, td {
        padding: 7px 5px;
        border-top: 1px solid #e2cee9;
        border-bottom: 1px solid #e2cee9;
    }
    th {
        white-space: nowrap;
        border-top: none;
        border-bottom: 2px solid #e2cee9;
        color: #7900a0;
        background-color: white
    }
    .pagination {
        position: sticky;
        left: 0;
    }
    button {
        background-color: #7900a0;
        color: #fff;
        font-weight: bold;
        padding: 7px 17px;
        border: 1px solid #7900a0;
        border-radius: 1px;
        margin-right: 2px;
        cursor: pointer;
    }
    button:disabled {
        background-color: #e2cee9;
        cursor: not-allowed;
    }
    button.selected {
        background-color: #fff;
        color: #7900a0;
    }
</style>
