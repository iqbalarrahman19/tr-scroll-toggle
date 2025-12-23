<template>
  <div class="header">
    <nav>
      <div
        v-for="(item, i) in steps"
        :key="i"
        class="nav-item"
        :class="{ active: i === activeStep }"
        @click="$emit('step-click', i)">
        {{ item.label }}

        <span class="progress" :style="progressStyle(i)" />
      </div>
    </nav>
  </div>
</template>

<script>
export default {
  name: "HeaderComp",

  props: {
    steps: Array,
    activeStep: Number,
    stepProgress: Number,
  },

  methods: {
    progressStyle(i) {
      // menu tidak aktif → tidak ada underline
      if (i !== this.activeStep) {
        return {
          transform: "scaleX(0)",
          opacity: 0,
        };
      }

      // SELALU kiri ➜ kanan
      return {
        transform: `scaleX(${this.stepProgress})`,
        transformOrigin: "left",
        opacity: 1,
      };
    },
  },
};
</script>

<style scoped lang="scss">
.header {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  padding: 2.5rem;
  z-index: 20;
}

nav {
  display: flex;
  gap: 1.8rem;
}

.nav-item {
  position: relative;
  font-family: "Inter", sans-serif;
  font-size: 2.8rem;
  font-weight: 400;
  text-transform: lowercase;
  opacity: 0.25;
  cursor: pointer;
  transition: opacity 0.4s ease;
}

.nav-item.active {
  opacity: 1;
}

.progress {
  position: absolute;
  left: 0;
  bottom: -6px;
  width: 100%;
  height: 3px;
  background: #c8ff5c;
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 0.6s cubic-bezier(0.22, 1, 0.36, 1);
}
</style>
