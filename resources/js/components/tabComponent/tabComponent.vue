<template>
  <div class="tab" v-show="isActive">
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'tabComponent',
  props: {
    name: {
      required: true,
    },
    selected: {
      default: false,
    }
  },
  data () {
    return {
      isActive: false,
    }
  },
  computed: {
    href() {
      return '#' + this.name.toLowerCase().replace(/ /g, '-')
    }
  },
  mounted() {
    this.isActive = this.selected
  }
}
</script>

<style lang="less" scoped>
</style>
