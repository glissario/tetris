<template>
  <div
    @click="testit"
    @keydown="testit"
    class="item"
    :style="{ backgroundColor: statusCheck }"
    ref="index"
  >
    {{}}
  </div>
</template>

<script>
export default {
  data() {
    return {
      wait: "gray",
      active: "",
      set: "hotpink",
      bgColor: "",
    };
  },
  /*watch: {
    activeStatus: {
      handler() {
        this.triggerMovement();
      },
      deep: true,
    },
  },*/
  created() {
    this.bgColor = this.wait;
  },

  props: {
    index: { Type: String, required: true },
    activeStatus: { Type: String, required: true },
  },
  computed: {
    statusCheck() {
      return this.activeStatus === "wait"
        ? "gray"
        : this.activeStatus === "set"
        ? this.activeStatus
        : this.activeStatus;
    },
  },
  methods: {
    testit() {
      console.log("down");
    },
  },
};
</script>
<style lang="scss">
.item {
  display: flex;
  align-items: center;
  justify-content: center;
  border: 0.25px dotted rgb(255, 255, 207);
}
</style>
