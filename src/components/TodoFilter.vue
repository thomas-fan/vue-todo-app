<template>
  <div>
    <button :class="{active: filter === 'all'}" @click="changeFilter('all')">全部</button>
    <button :class="{active: filter === 'active'}" @click="changeFilter('active')">进行中</button>
    <button :class="{active: filter === 'completed'}" @click="changeFilter('completed')">已完成</button>
  </div>
</template>

<script>
    export default {
        name: "TodoFilter",
        data() {
            return {
                'filter': 'all'
            }
        },
        methods: {
            changeFilter(filter) {
                this.filter = filter
                eventBus.$emit('filterChanged', filter)
            }
        }

    }
</script>

<style scoped>

</style>
