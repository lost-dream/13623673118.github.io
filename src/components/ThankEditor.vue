<template>
  <div class="thankEditor" :class="{ htmlMode: enableHtml }" ref="container">
    <div v-if="enableHtml" v-html="result"></div>
    <pre v-else>{{ result }}</pre>
  </div>
</template>

<script>
import marked from "marked";
export default {
  props: ["markdown", "enableHtml"],
  name: "ThankEditor",
  computed: {
    result: function() {
      return this.enableHtml ? marked(this.markdown) : this.markdown;
    }
  },
  methods: {
    goBottom() {
      this.$refs.container.scrollTop = 100000;
    },
    goTop() {
      this.$refs.container.scrollTop = 0;
    }
  }
};
</script>

<style scoped></style>
