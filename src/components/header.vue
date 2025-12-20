<template>
  <div class="header">
    <nav>
      <div
        v-for="(item, i) in steps"
        :key="i"
        class="nav-item"
        :class="{ active: i === activeStep }">
        {{ item.label }}

        <!-- HORIZONTAL PROGRESS (ONLY ACTIVE) -->
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
    isAfterLastStep: Boolean,
  },

  methods: {
    progressStyle(i) {
      if (i !== this.activeStep) {
        return {
          transform: "scaleX(0)",
          opacity: 0,
        };
      }

      return {
        transform: `scaleX(${this.stepProgress})`,
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
  padding: 2.5rem 2.5rem;
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
  letter-spacing: 0.02em;
  text-transform: lowercase;
  opacity: 0.25;
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
  transform-origin: left;
  transform: scaleX(0);
  opacity: 0;
  transition: transform 0.15s linear, opacity 0.2s ease;
}
</style>
