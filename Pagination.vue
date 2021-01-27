<template lang="pug">
    nav(v-if="total > perpage").nav-pagination
        ul.pagination.mar-0(:class="paginationClass")
            li(v-if="showNextPrev" :class="{ disabled: !hasPrev }")
                a(href="#" v-if="hasPrev" @click.prevent="changePage(prevPage)") Prev
                span(v-else) Prev
            li(v-if="hasFirst")
                a(href="#" @click.prevent="changePage(1)") 1

            li(v-if="hasFirst && rangeStart > 2").disabled
                span ...

            li(v-for="page in pages" v-bind:class="{ active: current == page }")
                span(v-if="current == page") {{ page | formatPageNumber }}
                a(v-else href="#" @click.prevent="changePage(page)") {{ page | formatPageNumber }}

            li(v-if="hasLast && rangeEnd < totalPages - 1").disabled
                span ...

            li(v-if="hasLast")
                a(href="#" @click.prevent="changePage(totalPages)") {{ totalPages | formatPageNumber }}
            li(v-if="showNextPrev" v-bind:class="{ disabled: !hasNext }")
                a(href="#" v-if="hasNext" @click.prevent="changePage(nextPage)") Next
                span(v-else) Next
</template>

<script>
export default {
    props: {
        current: {
            type: Number,
            default: 1
        },
        total: {
            type: Number,
            default: 0
        },
        inpage: { // object's count in page now
            type: Number,
            default: null
        },
        perpage: {
            type: Number,
            default: 10
        },
        pagerange: {
            type: Number,
            default: 2
        },
        size: {
            type: String,
            default: ''
        },
        showNextPrev: {
            type: Boolean,
            default: true
        }
    },
    computed: {
        pages() {
            var pages = []

            for (var i = this.rangeStart; i <= this.rangeEnd; i++) {
                pages.push(i);
            }

            return pages
        },
        useRange() {
            return this.totalPages > this.pagerange * 2 + 1
        },
        rangeStart() {
            var start = this.current - this.pagerange
            return start > 0 && this.useRange ? start : 1
        },
        rangeEnd() {
            var end = this.current + this.pagerange
            return end < this.totalPages && this.useRange ? end : this.totalPages
        },
        totalPages() {
            return Math.ceil(this.total / this.perpage)
        },
        nextPage() {
            return this.current + 1
        },
        prevPage() {
            return this.current - 1
        },
        paginationClass() {
            switch (this.size) {
                case 'small':
                    return 'pagination-sm'
                case 'sm':
                    return 'pagination-sm'
                case 'large':
                    return 'pagination-lg'
                case 'lg':
                    return 'pagination-lg'
                default:
                    return null
            }
        },
        hasFirst() {
            return this.rangeStart !== 1
        },
        hasLast() {
            return this.rangeEnd < this.totalPages
        },
        hasPrev() {
            return this.current > 1
        },
        hasNext() {
            return this.current < this.totalPages
        }
    },
    methods: {
        changePage(page) {
            this.$emit('page-changed', page)
        }
    },
    filters: {
        formatPageNumber(page) {
            return numeral(page).format('0,0')
        }
    },
    watch: {
        inpage() {
            // reload page if no items or prev page if it was last
            if (this.inpage === 0) {
                this.changePage(this.totalPages > 1 && this.current === this.totalPages ? this.totalPages - 1 :
                    (this.current > this.totalPages ? this.totalPages : this.current)
                )
            }
        }
    }
}
</script>
