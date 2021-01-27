<template lang="pug">
    div
        div(v-if="hasItems")
            slot(v-if="hasItems" name="nav" :next="next" :prev="prev" :current="current" :total="total" :per-page="perPage")
            slot(:items="itemsInPage" :total-pages="totalPages" :current="current" :per-page="perPage" :page-changed="onPageChanged" :set-current="setCurrent")

            pagination.mar-top-15(
                v-if="hasItems && showPagination"
                :class="classes"
                :size="size"
                :current="current"
                :total="items.length"
                :perpage="perPage"
                :show-next-prev="showNextPrev"
                @page-changed="onPageChanged")

            slot(v-if="hasItems" name="nav-bottom" :next="next" :prev="prev" :current="current" :total="total" :total-pages="totalPages" :per-page="perPage")

        slot(v-if="!hasItems && $slots.noitems" name="noitems")
        .text-center.text-muted.mar-30(v-else-if="!hasItems") No items
</template>

<script>
import Pagination from 'Pagination'

export default {
    components: {
        Pagination
    },
    props: {
        value: Number, // selected page
        perPage: {
            type: Number,
            default: 5
        },
        items: {
            type: Array,
            default() {
                return []
            }
        },
        showPagination: {
            type: Boolean,
            default: true
        },
        showNextPrev: Boolean,
        navPosition: {
            type: String,
            default: 'top'
        },
        // left, center, right
        align: {
            type: String,
            default: 'center'
        },

        // small, large
        size: String
    },
    data() {
        return {
            current: this.value || 1
        }
    },
    computed: {
        total() {
            return this.hasItems ? this.items.length : 0
        },
        totalPages() {
            return this.hasItems ? Math.ceil(this.items.length / this.perPage) : 0
        },
        hasItems() {
            return !!(this.items && this.items.length)
        },
        itemsInPage() {
            if (!this.hasItems) return []

            let start = (this.current - 1) * this.perPage,
                end = start + this.perPage
            return this.items.slice(start, end)
        },
        classes() {
            switch (this.align) {
                case 'center':
                    return 'text-center'
                case 'right':
                    return 'text-right'
                default:
                    return ''
            }
        }
    },
    methods: {
        next() {
            this.current = this.total ? this.current % this.total + 1 : 0
        },
        prev() {
            this.current = this.current === 1 ? this.total : this.current - 1
        },
        onPageChanged(page) {
            this.current = page
            this.$emit('changed', page)
            this.$emit('input', page)
        },
        setCurrent(page) {
            this.current = page < 1 ? 1 : (page > this.total ? this.total : page)
        },
    },
    watch: {
        totalPages(newNumber, oldNumber) {
            this.current = this.current > newNumber
                ? newNumber
                : (this.current || 1)
        },
        value(page) {
            this.setCurrent(page)
        },
    },
    created() {
        this.current = Number(!!this.total)
    }
}
</script>
