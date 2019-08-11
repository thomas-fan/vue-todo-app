<template>
  <div>
    剩下{{remaining}}个项目
  </div>
</template>

<script>
    export default {
        name: "TodoItemsRemaining",
        props: {
            remaining: {
                type: Number,
                required: true,
            }
        }
    }
</script>

<style scoped>

</style>
