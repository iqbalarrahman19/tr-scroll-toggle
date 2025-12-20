<template>
  <section>
    <!-- ================= DESKTOP / TABLET ================= -->
    <section class="section-one">
      <!-- NAVBAR -->
      <HeaderComp
        :steps="steps"
        :activeStep="activeStep"
        :stepProgress="stepProgress"
        :isAfterLastStep="isAfterLastStep" />

      <!-- LEFT -->
      <div class="panel left">
        <div class="text">
          <h1>
            <span v-for="(word, i) in splitTitle" :key="i" class="line">
              {{ word }}
            </span>
          </h1>
          <p ref="paragraph">{{ steps[activeStep].desc }}</p>
        </div>
      </div>

      <!-- RIGHT -->
      <div class="panel right">
        <div ref="counter" class="counter">
          {{ activeStep + 1 }} / {{ steps.length }}
        </div>

        <div class="media">
          <!-- progress vertical -->
          <div class="progress-line">
            <div class="track"></div>
            <div class="active"></div>
          </div>

          <!-- IMAGE -->
          <div class="image-container">
            <img
              ref="mainImage"
              :src="steps[activeStep].image"
              class="main-image" />
          </div>

          <!-- ICON -->
          <div ref="icon" class="icon">
            <img :src="steps[activeStep].icon" />
          </div>
        </div>
      </div>
    </section>

    <!-- ================= MOBILE / ANDROID ================= -->
    <section class="mobile-stack">
      <article
        v-for="(step, i) in steps"
        :key="'mobile-' + i"
        class="mobile-card">
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
          <img :src="step.image" />
        </div>
      </article>
    </section>
  </section>
</template>

<script>
import HeaderComp from "./header.vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

export default {
  name: "SectionOne",
  components: { HeaderComp },

  data() {
    return {
      activeStep: 0,
      stepProgress: 0,
      isAfterLastStep: false,
      steps: [
        {
          label: "plan",
          title: "The sitemap of the experience",
          desc: "Lorem ipsum dolor sit amet consectetur adipiscing elit quisque faucibus ex sapien vitae pellentesque sem placerat in id cursus mi pretium tellus duis convallis tempus leo eu aenean sed diam urna tempor pulvinar vivamus fringilla lacus nec metus bibendum egestas.",
          image: require("@/assets/images/imgi_2_638e3f15b3ed3463ebe6038b_pexels-wendy-wei-14397945.jpg"),
          icon: require("@/assets/images/imgi_1_638e411bd0e9dd70ed4f30e8_plan.svg"),
        },
        {
          label: "design",
          title: "Time to paint the room walls",
          desc: "Lorem ipsum dolor sit amet consectetur adipiscing elit quisque faucibus ex sapien vitae pellentesque sem placerat in id cursus mi pretium tellus duis convallis tempus leo eu aenean sed diam urna tempor pulvinar vivamus fringilla.",
          image: require("@/assets/images/imgi_4_638e4092e9575c0f9629ae01_walls.jpg"),
          icon: require("@/assets/images/imgi_3_638e3f259cd4ab766024d0e3_icon.svg"),
        },
        {
          label: "build",
          title: "Let the code bring it alive",
          desc: "Lorem ipsum dolor sit amet consectetur adipiscing elit quisque faucibus ex sapien vitae pellentesque sem placerat in id cursus mi pretium tellus duis convallis tempus leo eu aenean sed diam.",
          image: require("@/assets/images/imgi_6_638e45c467fd8f44a5687f97_pexels-cottonbro-studio-5474032.jpg"),
          icon: require("@/assets/images/imgi_5_638e45c4ee2e7c91334d22ba_code.svg"),
        },
      ],
    };
  },

  computed: {
    splitTitle() {
      return this.steps[this.activeStep].title.split(" ");
    },
  },

  mounted() {
    // ⛔ JANGAN aktifkan GSAP di mobile
    if (window.innerWidth <= 768) return;

    ScrollTrigger.create({
      trigger: this.$el.querySelector(".section-one"),
      start: "top top",
      end: "+=300%",
      scrub: 0.5,
      pin: true,

      onUpdate: (self) => {
        const total = this.steps.length;
        const exact = self.progress * total;
        const index = Math.min(total - 1, Math.floor(exact));
        const stepProgress = Math.min(1, Math.max(0, exact - index));

        // 🔥 DETECT LAST STEP PASSED
        if (self.progress >= 1) {
          this.isAfterLastStep = true;
        } else if (self.direction === -1) {
          // scroll ke atas → reset flag
          this.isAfterLastStep = false;
        }

        gsap.set(this.$el.querySelector(".progress-line .active"), {
          height: `${self.progress * 100}%`,
        });

        gsap.to(this.$refs.mainImage, {
          scale: 1.15 - stepProgress * 0.15,
          duration: 0.3,
          overwrite: "auto",
        });

        gsap.to(this.$refs.icon, {
          scale: 1.25 + stepProgress * 0.15,
          duration: 0.3,
          overwrite: "auto",
        });

        this.activeStep = index;
        this.stepProgress = stepProgress;
      },
    });

    this.animateText();
  },

  watch: {
    activeStep() {
      this.$nextTick(this.animateText);
    },
  },

  methods: {
    animateText() {
      const lines = this.$el.querySelectorAll(".line");
      const paragraph = this.$refs.paragraph;

      gsap.killTweensOf([lines, paragraph]);

      gsap.fromTo(
        lines,
        { yPercent: 120 },
        {
          yPercent: 0,
          stagger: 0.06,
          duration: 0.8,
          ease: "power3.out",
        }
      );

      gsap.fromTo(
        paragraph,
        { y: 20 },
        {
          y: 0,
          duration: 0.6,
          delay: 0.2,
          ease: "power3.out",
        }
      );
    },
  },
};
</script>

<style scoped lang="scss">
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
  right: 40%;
  width: 110px;
  transform: translateY(-50%);
}

/* ================= MOBILE STACK ================= */
.mobile-stack {
  display: none;
  background: #0c1408;
  padding: 2rem 1.25rem;
}

.mobile-card {
  margin-bottom: 4rem;
}

.mobile-icon {
  width: 28px;
  margin-bottom: 1rem;
}

.mobile-icon img {
  width: 100%;
}

.mobile-title {
  font-family: "Playfair Display", serif;
  font-style: italic;
  font-size: 2rem;
  line-height: 1;
  color: #c6fb50;
  margin-bottom: 1rem;
}

.mobile-desc {
  font-size: 0.85rem;
  line-height: 1.6;
  color: #c6fb50;
  opacity: 0.9;
  margin-bottom: 1.5rem;
  width: 17rem;
}

.mobile-image {
  border-radius: 12px;
  max-height: 200px;
  overflow: hidden;
}

.mobile-image img {
  width: 100%;
  display: block;
  object-fit: contain;
}

/* ================= BREAKPOINT ================= */
@media (max-width: 768px) {
  .section-one {
    display: none;
  }

  .mobile-stack {
    display: block;
  }
}
</style>
