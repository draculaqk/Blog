<template>
    <div id='main'>
        <section id="page-index">
            <blog-summary v-for="item in items" :article="item"></blog-summary>
            <pagination :page="page" :total-page="totalPage"></pagination>
        </section>
        <my-footer></my-footer>
    </div>
</template>

<script>
/* eslint-disable */
import store from '../store/api'

function fetchItems (serverStore, { path, query, params}) {
    if (path != '/'){
        return resolve()
    }

    let page = (typeof query.page !== 'undefined') ? parseInt(query.page) : 1; 
    if (page < 0 ){
        page = 1;
    }

    return serverStore.dispatch('FETCH_ITEMS',{
            conditions: { 
                type: 0 
            },
            select: {
                title: 1,
                summary: 1,
                commentNum: 1,
                createdAt: 1,
                pathName: 1,
            },
            limit: 10,
            skip: (page - 1) * 10,
            sort: 1,
        }
    )


}

export default {
    data () {
        const isInitialRender = !this.$root._isMounted
        return {
            totalPage: 1,
        }
    },
    computed:{
        items() {
            return this.$store.getters.items;
        },
        page() {
            let page = this.$store.state.route.query.page || 1;
            return parseInt(page);
        },
        totalPage() {
            return this.$store.state.totalPage;
        }
    },
    beforeMount () {
        if (this.$root._isMounted){
            fetchItems(this.$store, this.$store.state.route)
        }
    },
    preFetch: fetchItems,
    watch: {
        '$route': 'fetchData'
    },
    methods: {
        fetchData: function(val, oldVal) {
            
            if (val.name != 'main')
                return 

            fetchItems(this.$store, this.$store.state.route)

        }
    },


}
</script>