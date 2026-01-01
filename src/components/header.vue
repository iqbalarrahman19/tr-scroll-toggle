<template>
  <div class="header">
    <nav>
      <div
        v-for="(item, i) in steps"
        :key="i"
        ref="items"
        class="nav-item"
        :class="{ active: i === activeStep }"
        @click="$emit('step-click', i)">
        {{ item.label }}
      </div>

      <!-- SHARED UNDERLINE -->
      <span class="underline" ref="underline" />
    </nav>
  </div>
</template>

<script>
import { gsap } from "gsap";

export default {
  name: "HeaderComp",

  props: {
    steps: Array,
    activeStep: Number,
    stepProgress: Number,
    direction: Number, // 🔥 dari parent
  },

  watch: {
    activeStep() {
      this.animateIn();
    },

    stepProgress(v) {
      // grow progress saat scroll di dalam step
      gsap.set(this.$refs.underline, {
        scaleX: Math.max(v, 0.001),
      });
    },
  },

  mounted() {
    this.$nextTick(() => {
      this.animateIn(true);
    });
  },

  methods: {
    animateIn(immediate = false) {
      const item = this.$refs.items[this.activeStep];
      if (!item) return;

      const underline = this.$refs.underline;

      const x = item.offsetLeft;
      const width = item.offsetWidth;

      const origin = this.direction === -1 ? "left" : "left";

      // pindahkan underline ke menu baru
      gsap.set(underline, {
        x,
        width,
        scaleX: 0,
        transformOrigin: origin,
      });

      // grow underline sesuai arah
      gsap.to(underline, {
        scaleX: 1,
        duration: immediate ? 0 : 0.45,
        ease: "power3.out",
      });
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
  position: relative;
  display: flex;
  gap: 1.8rem;
}

.nav-item {
  font-family: "Inter", sans-serif;
  font-size: 2.8rem;
  text-transform: lowercase;
  opacity: 0.25;
  cursor: pointer;
  transition: opacity 0.4s ease;
}

.nav-item.active {
  opacity: 1;
}

.underline {
  position: absolute;
  bottom: -6px;
  left: 0;
  height: 3px;
  background: #c8ff5c;
  transform-origin: left;
  will-change: transform, width;
}
</style>
