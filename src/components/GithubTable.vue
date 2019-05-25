<template>
    <PaginatedTable
        :api-url="apiUrl"
        :response-function="jsonResponse => jsonResponse.items"
        :columns="columns">
            <template v-slot:owner="prop">
                <div class="nowrap break-all">
                    <a :href="prop.item.html_url">
                        <img :src="prop.item.owner.avatar_url" class="icon">
                        {{ prop.item.full_name }}
                    </a>
                </div>
            </template>
            <template v-slot:created_at="prop">
                <div class="nowrap">
                    {{ prop.item.created_at | date }}
                </div>
            </template>
            <template v-slot:updated_at="prop">
                <div class="nowrap">
                    {{ prop.item.updated_at | dateTime }}
                </div>
            </template>
    </PaginatedTable>
</template>

<script>
import PaginatedTable from './PaginatedTable'

export default {
    data() {
        return {
            apiUrl: 'https://api.github.com/search/repositories?q=language:javascript&sort=stars&order=desc&per_page=100',
            columns: {
                owner: 'Owner',
                description: 'Description',
                language: 'Language',
                created_at: 'Date created',
                updated_at: 'Last update'
            },
        }
    },
    name: 'GithubTable',
    components: {
        PaginatedTable
    },
    filters: {
        date(input) {
            let date = new Date(input)
            return date.toLocaleDateString()
        },
        dateTime(input) {
            let date = new Date(input)
            return `${date.toLocaleDateString()} ${date.toLocaleTimeString()}`
        }
    }
}
</script>

<style scoped>
img.icon {
    max-height: 25px;
    max-width: 25px;
    vertical-align: middle;
}
.nowrap {
    white-space: nowrap;
}
.break-all {
    word-break: break-all;
}
</style>
