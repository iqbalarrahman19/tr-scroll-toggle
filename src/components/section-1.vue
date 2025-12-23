<template>
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
          <img
            ref="mainImage"
            :src="steps[activeStep].image"
            class="main-image" />
        </div>

        <div ref="icon" class="icon">
          <img :src="steps[activeStep].icon" />
        </div>
      </div>
    </div>
  </section>
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
      // textMode: "word",

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

  // computed: {
  //   splitTitle() {
  //     return this.steps[this.activeStep].title.split(" ");
  //   },
  // },

  mounted() {
    if (window.innerWidth <= 768) return;

    this.runIntroAnimation();

    ScrollTrigger.create({
      trigger: this.$el,
      start: "top top",
      end: "+=300%",
      scrub: true,
      pin: true,

      onUpdate: (self) => {
        const total = this.steps.length;
        const exact = self.progress * total;
        const index = Math.min(total - 1, Math.floor(exact));
        const local = exact - index;

        this.direction = self.direction; // 1 = down, -1 = up
        this.interaction = "scroll";

        if (index !== this.activeStep) {
          this.prevStep = this.activeStep;
        }

        this.activeStep = index;
        this.stepProgress = local;

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
    });
  },

  methods: {
    runIntroAnimation() {
      const tl = gsap.timeline({ defaults: { ease: "power3.out" } });

      gsap.set(".progress-line .active", { height: "0%" });
      gsap.set(this.$refs.mainImage, { scale: 1.25 });
      gsap.set(this.$refs.icon, { scale: 0.6 });
      gsap.set(".line", { yPercent: 120, opacity: 0 });
      gsap.set(this.$refs.paragraph, { opacity: 0, y: 20 });

      tl.to(".progress-line .active", {
        height: `${(1 / this.steps.length) * 100}%`,
        duration: 1,
      })
        .to(this.$refs.mainImage, { scale: 1, duration: 1 }, "<")
        .to(this.$refs.icon, { scale: 1.35, duration: 0.8 }, "<0.1")
        .to(
          ".line",
          { yPercent: 0, stagger: 0.06, duration: 0.8, opacity: 1 },
          "<0.2"
        )
        .to(this.$refs.paragraph, { opacity: 1, y: 0, duration: 0.6 }, "<0.1");

      tl.eventCallback("onUpdate", () => {
        this.stepProgress = tl.progress();
      });

      tl.eventCallback("onComplete", () => {
        this.stepProgress = 1;
      });
    },

    goToStep(index) {
      const total = this.steps.length;
      const target = (index + 1) / total - 0.001;

      this.prevStep = this.activeStep;
      this.activeStep = index;
      this.direction = index > this.prevStep ? 1 : -1;
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
      });
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
  right: 30%;
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
