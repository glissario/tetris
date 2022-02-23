<template>
  <div
    @click="testit"
    @keydown="testit"
    class="item"
    :style="{ backgroundColor: statusCheck }"
    ref="index"
  >
    {{ index }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      wait: "gray",
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
    itemColor: { Type: String, required: true },
    active: { Type: Boolean, required: true },
  },
  computed: {
    statusCheck() {
      return this.itemColor === "wait" ? "gray" : this.itemColor;
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
