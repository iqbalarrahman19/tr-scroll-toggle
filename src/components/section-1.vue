<template>
  <div>
    <section class="section-one">
      <HeaderComp
        :steps="steps"
        :activeStep="activeStep"
        :stepProgress="stepProgress"
        :direction="direction"
        :interaction="interaction"
        :prevStep="prevStep"
        @step-click="goToStep" />

      <div class="panel left">
        <div class="text">
          <h1>
            <span class="line">
              {{ steps[activeStep].title }}
            </span>
          </h1>

          <p ref="paragraph">
            {{ steps[activeStep].desc }}
          </p>
        </div>
      </div>

      <div class="panel right">
        <div class="counter">{{ activeStep + 1 }} / {{ steps.length }}</div>
        <div class="media">
          <div class="progress-line">
            <div class="track"></div>
            <div class="active"></div>
          </div>

          <div class="image-container">
            <img ref="mainImage" :src="currentImage" class="main-image" />
          </div>

          <div ref="icon" class="icon">
            <img :src="steps[activeStep].icon" />
          </div>
        </div>
      </div>
    </section>
    <!-- ================= MOBILE STACK ================= -->
    <section class="mobile-stack">
      <div v-for="(step, i) in steps" :key="i" class="mobile-card">
        <div class="mobile-icon">
          <img :src="step.icon" />
        </div>

        <h2 class="mobile-title">
          {{ step.title }}
        </h2>

        <p class="mobile-desc">
          {{ step.desc }}
        </p>

        <div class="mobile-image">
          <img :src="step.image.mobile" />
        </div>
      </div>
    </section>
    <!-- ================= TABLET STACK ================= -->
    <section class="tablet-stack-wf">
      <div v-for="(step, i) in steps" :key="i" class="tablet-wf-card">
        <div class="tablet-wf-text">
          <div class="tablet-wf-icon">
            <img :src="step.icon" />
          </div>

          <h2 class="tablet-wf-title">
            {{ step.title }}
          </h2>

          <p class="tablet-wf-desc">
            {{ step.desc }}
          </p>
        </div>

        <div class="tablet-wf-image">
          <img :src="step.image.tablet" />
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import HeaderComp from "./header.vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { ScrollToPlugin } from "gsap/ScrollToPlugin";

gsap.registerPlugin(ScrollTrigger, ScrollToPlugin);

export default {
  components: { HeaderComp },

  data() {
    return {
      activeStep: 0,
      prevStep: null,
      stepProgress: 0,
      direction: 1,
      interaction: "scroll",
      st: null,

      // 🔥 LOCK SAAT CLICK
      isClicking: false,

      steps: [
        {
          label: "plan",
          title: "The sitemap of the experience",
          desc: "Lorem ipsum dolor sit amet consectetur adipiscing elit quisque faucibus ex sapien vitae pellentesque sem placerat in id cursus mi pretium tellus duis convallis tempus leo eu aenean sed diam urna tempor pulvinar vivamus fringilla lacus nec metus bibendum egestas.",
          image: {
            desktop: require("@/assets/images/imgi_2_638e3f15b3ed3463ebe6038b_pexels-wendy-wei-14397945.jpg"),
            tablet: require("@/assets/images/imgi_13_638e3f15b3ed3463ebe6038b_pexels-wendy-wei-14397945-p-800.jpg"),
            mobile: require("@/assets/images/imgi_16_638e3f15b3ed3463ebe6038b_pexels-wendy-wei-14397945-p-2600.jpg"),
          },
          icon: require("@/assets/images/imgi_1_638e411bd0e9dd70ed4f30e8_plan.svg"),
        },
        {
          label: "design",
          title: "Time to paint the room walls",
          desc: "Lorem ipsum dolor sit amet consectetur adipiscing elit quisque faucibus ex sapien vitae pellentesque sem placerat in id cursus mi pretium tellus duis convallis tempus leo eu aenean sed diam urna tempor pulvinar vivamus fringilla.",
          image: {
            desktop: require("@/assets/images/imgi_4_638e4092e9575c0f9629ae01_walls.jpg"),
            tablet: require("@/assets/images/imgi_18_638e4092e9575c0f9629ae01_walls-p-800.jpg"),
            mobile: require("@/assets/images/imgi_20_638e4092e9575c0f9629ae01_walls-p-1600.jpg"),
          },
          icon: require("@/assets/images/imgi_3_638e3f259cd4ab766024d0e3_icon.svg"),
        },
        {
          label: "build",
          title: "Let the code bring it alive",
          desc: "Lorem ipsum dolor sit amet consectetur adipiscing elit quisque faucibus ex sapien vitae pellentesque sem placerat in id cursus mi pretium tellus duis convallis tempus leo eu aenean sed diam.",
          image: {
            desktop: require("@/assets/images/imgi_6_638e45c467fd8f44a5687f97_pexels-cottonbro-studio-5474032.jpg"),
            tablet: require("@/assets/images/imgi_21_638e45c467fd8f44a5687f97_pexels-cottonbro-studio-5474032-p-800.jpg"),
            mobile: require("@/assets/images/imgi_24_638e45c467fd8f44a5687f97_pexels-cottonbro-studio-5474032-p-2600.jpg"),
          },
          icon: require("@/assets/images/imgi_5_638e45c4ee2e7c91334d22ba_code.svg"),
        },
      ],
    };
  },

  computed: {
    currentImage() {
      const img = this.steps[this.activeStep].image;

      if (window.innerWidth <= 768) {
        return img.mobile;
      }

      if (window.innerWidth <= 1024) {
        return img.tablet;
      }

      return img.desktop;
    },
    currentIcon() {
      const icon = this.steps[this.activeStep].icon;

      if (window.innerWidth <= 768) {
        return icon.mobile;
      }

      if (window.innerWidth <= 1024) {
        return icon.tablet;
      }

      return icon.desktop;
    },
  },

  mounted() {
    if (window.innerWidth <= 768) return;
    if (window.innerWidth <= 1024) return;

    // ===============================
    // 1️⃣ BUAT ScrollTrigger
    // ===============================
    this.st = ScrollTrigger.create({
      trigger: this.$el,
      start: "top top",
      end: "+=300%",
      scrub: true,
      pin: true,
      onUpdate: (self) => {
        this.updateByScroll(self);
        if (this.interaction === "click") return;

        const total = this.steps.length;
        const exact = self.progress * total;
        const index = Math.min(total - 1, Math.floor(exact));
        const local = exact - index;

        this.direction = self.direction;
        this.activeStep = index;
        this.stepProgress = local;
      },
    });

    ScrollTrigger.refresh();

    // ===============================
    // 2️⃣ RESTORE STEP (WEBFLOW STYLE)
    // ===============================
    const savedStep = sessionStorage.getItem("wf-step");
    if (savedStep !== null) {
      this.goToStep(Number(savedStep));
    }
  },

  methods: {
    // ===============================
    // UPDATE SAAT SCROLL
    // ===============================
    updateByScroll(self) {
      const total = this.steps.length;
      const exact = self.progress * total;
      const index = Math.min(total - 1, Math.floor(exact));
      const local = exact - index;

      this.direction = self.direction;
      this.interaction = "scroll";

      if (index !== this.activeStep) {
        this.prevStep = this.activeStep;
      }

      this.activeStep = index;
      this.stepProgress = local;

      // 🔥 SIMPAN STEP (KUNCI WEBFLOW)
      sessionStorage.setItem("wf-step", index);

      gsap.set(".progress-line .active", {
        height: `${self.progress * 100}%`,
      });

      gsap.set(this.$refs.mainImage, {
        scale: 1.15 - local * 0.15,
      });

      gsap.set(this.$refs.icon, {
        scale: 1.25 + local * 0.45,
      });
    },

    goToStep(index) {
      const total = this.steps.length;
      const target = (index + 1) / total - 0.001;

      this.prevStep = this.activeStep;
      this.activeStep = index;
      this.direction = index > this.prevStep ? 1 : -1;

      // 🔥 reset progress
      this.stepProgress = 0;
      this.interaction = "click";

      gsap.to(window, {
        scrollTo: {
          y:
            ScrollTrigger.getAll()[0].start +
            (ScrollTrigger.getAll()[0].end - ScrollTrigger.getAll()[0].start) *
              target,
        },
        duration: 1.2,
        ease: "power3.inOut",

        onComplete: () => {
          // 🔓 balik ke scroll mode
          this.interaction = "scroll";
        },
      });
    },
  },
};
</script>

<style scoped lang="scss">
/* STYLE TIDAK DIUBAH */
.section-one {
  height: 100vh;
  display: flex;
  background: #0c1408;
  color: #c6fb50;
  font-family: "Inter", sans-serif;
}

.panel {
  width: 50%;
  position: relative;
}

.left {
  padding: 12rem 2.5rem 0 2.5rem;
  display: flex;
  align-items: center;
}

h1 {
  font-family: "Playfair Display", "Times New Roman", serif;
  font-size: 4.8rem;
  font-style: italic;
  font-weight: 300;
  line-height: 0.75;
  margin-bottom: 10px;
  width: 90%;
}

.line {
  display: inline-block;
  margin-right: 0.25em;
}

p {
  margin-top: 2rem;
  font-size: 0.95rem;
  color: #c6fb50;
  width: 70%;
}

.right {
  display: flex;
  align-items: center;
  justify-content: center;
}

.counter {
  position: absolute;
  top: 3.5rem;
  right: 4.5rem;
  font-size: 2.5rem;
  font-style: italic;
  z-index: 5;
}

.media {
  width: 100%;
  height: 100%;
  position: relative;
}

.image-container {
  width: 100%;
  height: 100%;
  overflow: hidden;
  border-radius: 10% 0 0 10%;
}

.main-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.progress-line {
  position: absolute;
  right: 0;
  top: 0;
  width: 4px;
  height: 100%;
  z-index: 10;
}

.track {
  height: 100%;
  background: rgba(200, 255, 92, 0.15);
}

.active {
  position: absolute;
  top: 0;
  width: 100%;
  background: #c6fb50;
}

.icon {
  position: absolute;
  top: 50%;
  right: 30%;
  width: 110px;
  transform: translateY(-50%);
}

/* ================= MOBILE STACK ================= */
.mobile-stack {
  display: none;
  background: #0c1408;
  padding: 2.5rem 1.5rem;
}

.mobile-card {
  margin-bottom: 5rem;
}

.mobile-icon {
  width: 18px;
}

.mobile-icon img {
  width: 100%;
}

.mobile-title {
  font-family: "Playfair Display", serif;
  font-style: italic;
  font-size: 1rem;
  line-height: 1;
  color: #c6fb50;
}

.mobile-desc {
  font-size: 0.85rem;
  line-height: 1.6;
  color: #c6fb50;
  opacity: 0.9;
  margin-bottom: 1.75rem;
  max-width: 18rem;
}

.mobile-image {
  border-radius: 14px;
  overflow: hidden;
}

.mobile-image img {
  width: 100%;
  display: block;
  object-fit: cover;
  height: 20rem;
}

/* ================= TABLET STACK ================= */
.tablet-stack-wf {
  display: none;
  background: #0c1408;
  padding: 3.5rem 0rem;
}

.tablet-wf-card {
  margin-bottom: 7rem;
}

.tablet-wf-text {
  width: 90%;
  margin: 0 auto 2rem auto;
}

.tablet-wf-icon {
  width: 60px;
}

.tablet-wf-icon img {
  width: 100%;
}

.tablet-wf-title {
  font-family: "Playfair Display", serif;
  font-style: italic;
  font-size: 64px;
  line-height: 0.9;
  font-weight: 300;
  color: #c6fb50;
  width: 25rem;
}

.tablet-wf-desc {
  font-size: 16px;
  line-height: 1.7;
  color: #c6fb50;
  max-width: 26rem;
  opacity: 0.9;
}

/* IMAGE FULL WIDTH */
.tablet-wf-image {
  width: 100%;
  overflow: hidden;
}

.tablet-wf-image img {
  width: 46rem;
  height: 26rem;
  display: block;
  object-fit: cover;
  margin-left: 2rem;
  margin-right: 2rem;
}

/* ================= DESKTOP ================= */
@media (min-width: 1025px) {
  .section-one {
    display: flex;
  }
}

/* ================= TABLET ================= */
@media (max-width: 1024px) and (min-width: 769px) {
  .section-one {
    display: none;
  }

  .mobile-stack {
    display: none;
  }

  .tablet-stack-wf {
    display: block;
  }
}

/* ================= MOBILE ================= */
@media (max-width: 768px) {
  .section-one {
    display: none;
  }

  .mobile-icon {
    scale: 2;
    margin-left: 1rem;
  }

  .mobile-title {
    width: 90%;
    font-size: 45px;
    margin-top: 20px;
    margin-bottom: -20px;
  }
  .mobile-desc {
    font-size: 15px;
    min-width: 18rem;
  }

  .tablet-stack-wf {
    display: none;
  }

  .mobile-stack {
    display: block;
  }
}
</style>
