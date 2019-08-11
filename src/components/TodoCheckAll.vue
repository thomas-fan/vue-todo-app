<template>
  <div>
    <label>
      <input type="checkbox"
             :checked="!anyRemaining"
             @change="allChecked"
      >
      全选
    </label>
  </div>
</template>

<script>
    export default {
        name: "TodoCheckAll",
        props: {
            anyRemaining: {
                type: Boolean,
                required: true,
            }
        },
        methods: {
            allChecked() {
                eventBus.$emit('checkAllChanged', this.anyRemaining)
            }

        }

    }
</script>

<style scoped>

</style>
