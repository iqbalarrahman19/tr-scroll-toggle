<template>
  <div class="fade-item">
    <slot />
  </div>
</template>

<script>
export default {
  name: "FadeItem",
  mounted() {
    this.$gsap.from(this.$el, {
      opacity: 0,
      y: 40,
      duration: 0.8,
      scrollTrigger: {
        trigger: this.$el,
        start: "top 80%",
      },
    });
  },
};
</script>

<style scoped>
.fade-item {
  will-change: opacity, transform;
}
</style>
