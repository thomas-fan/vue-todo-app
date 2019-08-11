<template>
  <button v-if="showClearCompletedButton"
          @click="clearCompleted"
  >清除已完成
  </button>
</template>

<script>
    export default {
        name: "TodoClearCompleted",
        props: {
            showClearCompletedButton: {
                type: Boolean,
                required: true
            }
        },
        methods: {
            clearCompleted(){
              eventBus.$emit('clearCompletedTodos')
            }
        }
    }
</script>

<style scoped>

</style>
