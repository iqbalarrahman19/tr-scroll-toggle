<template>
  <section class="scroll-toggle">
    <!-- ================= TOP PROGRESS ================= -->
    <div class="top-progress">
      <div class="progress-track"></div>
      <div class="progress-active" ref="progress"></div>
    </div>

    <div class="inner">
      <!-- ================= COUNTER ================= -->
      <div class="counter">{{ activeIndex + 1 }} — {{ items.length }}</div>

      <!-- ================= WORDS ================= -->
      <div class="words">
        <h1 v-for="(item, i) in items" :key="i" class="word" ref="words">
          {{ item.label }}
        </h1>
      </div>

      <!-- ================= IMAGES ================= -->
      <div class="image-wrap">
        <img
          v-for="(item, i) in items"
          :key="i"
          :src="item.image"
          class="image"
          ref="images"
          loading="lazy"
          decoding="async" />
      </div>

      <button class="button">Learn More</button>
    </div>
  </section>
</template>

<script>
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

// Global GSAP defaults (smooth scrub)
gsap.defaults({
  ease: "none",
});

export default {
  name: "SectionTwo",

  data() {
    return {
      activeIndex: 0,
      items: [
        {
          label: "plan",
          image: require("@/assets/images/imgi_17_638e3f15b3ed3463ebe6038b_pexels-wendy-wei-14397945-p-3200.jpg"),
        },
        {
          label: "design",
          image: require("@/assets/images/imgi_20_638e4092e9575c0f9629ae01_walls-p-1600.jpg"),
        },
        {
          label: "build",
          image: require("@/assets/images/imgi_25_638e45c467fd8f44a5687f97_pexels-cottonbro-studio-5474032-p-3200.jpg"),
        },
      ],
    };
  },

  mounted() {
    // Respect reduced motion preference
    if (window.matchMedia("(prefers-reduced-motion: reduce)").matches) return;

    const words = this.$refs.words;
    const images = this.$refs.images;
    const progress = this.$refs.progress;
    const total = this.items.length;

    /* ================= INITIAL STATE ================= */

    // TEXT
    gsap.set(words, {
      xPercent: -50,
      yPercent: 120,
    });
    gsap.set(words[0], { yPercent: 0 });

    // IMAGES
    gsap.set(images, {
      scale: 0,
      transformOrigin: "50% 50%",
    });

    // PROGRESS BAR
    gsap.set(progress, {
      scaleX: 0,
      transformOrigin: "left",
    });

    /* ================= TIMELINE ================= */

    const tl = gsap.timeline({
      scrollTrigger: {
        trigger: this.$el,
        start: "top top",
        end: `+=${total * 120}%`,
        scrub: true,
        pin: true,
        anticipatePin: 1, // Safari & mobile stability
        onUpdate: (self) => {
          const index = Math.min(total - 1, Math.floor(self.progress * total));
          this.activeIndex = index;
        },
      },
    });

    /* ================= PROGRESS (GLOBAL) ================= */
    tl.to(
      progress,
      {
        scaleX: 1,
        duration: total,
      },
      0
    );

    /* ================= STEPS ================= */

    this.items.forEach((_, i) => {
      const imageStart = i;
      const imageFull = i + 0.7;
      const nextText = i + 1;

      // IMAGE SCALE IN
      tl.fromTo(
        images[i],
        { scale: 0 },
        { scale: 1, duration: 0.7 },
        imageStart
      );

      // TEXT TRANSITION
      if (nextText < total) {
        // Current text out
        tl.to(
          words[i],
          {
            yPercent: -120,
            duration: 0.3,
            ease: "power2.out",
          },
          imageFull
        );

        // Next text in
        tl.to(
          words[nextText],
          {
            yPercent: 0,
            duration: 0.4,
            ease: "power3.out",
            onStart: () => (this.activeIndex = nextText),
          },
          imageFull
        );
      }
    });
  },

  beforeDestroy() {
    // Clean up ScrollTrigger (SPA safety)
    ScrollTrigger.getAll().forEach((t) => t.kill());
  },
};
</script>

<style scoped lang="scss">
/* ================= ROOT ================= */
.scroll-toggle {
  height: 100vh;
  background: radial-gradient(circle at center, #101a08 0%, #0c1408 70%);
  color: #c6fb50;
  position: relative;
  overflow: hidden;
}

/* ================= PERFORMANCE HINT ================= */
.word,
.image,
.progress-active {
  will-change: transform;
}

/* ================= PROGRESS ================= */
.top-progress {
  position: absolute;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 100%;
  height: 3px;
  z-index: 10;
}

.progress-track {
  position: absolute;
  inset: 0;
  background: rgba(200, 255, 92, 0.2);
  opacity: 0;
}

.progress-active {
  position: absolute;
  inset: 0;
  background: #c6fb50;
}

/* ================= CENTER ================= */
.inner {
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.counter {
  font-size: 0.85rem;
  letter-spacing: 0.2em;
  margin-bottom: 2.5rem;
  z-index: 5;
}

/* ================= TEXT ================= */
.words {
  position: relative;
  width: 100%;
  height: clamp(8rem, 30vw, 16rem);
  overflow: hidden;
  z-index: 10;
}

.word {
  position: absolute;
  left: 50%;
  font-family: "Playfair Display", serif;
  font-style: italic;
  font-size: clamp(6rem, 18vw, 14rem);
  line-height: 1;
  white-space: nowrap;
  margin: 0;
  font-weight: 200;
}

/* ================= IMAGE ================= */
.image-wrap {
  position: absolute;
  inset: 0;
  z-index: 1;
}

.image {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* ================= BUTTON ================= */
.button {
  position: absolute;
  bottom: 4rem;
  padding: 1rem 2rem;
  font-size: 1rem;
  border: 2px solid #c6fb50;
  font-weight: 600;
  color: #c6fb50;
  border-radius: 50px;
  cursor: pointer;
  z-index: 10;
  background: transparent;
}

/* ================= TABLET RESPONSIVE ================= */
@media (min-width: 768px) and (max-width: 1024px) {
  .inner {
    padding: 0 3rem;
  }

  /* Text container lebih pendek agar tidak kepotong */
  .words {
    height: clamp(10rem, 28vw, 10rem);
  }

  /* Ukuran font tetap besar tapi lebih stabil */
  .word {
    font-size: clamp(5rem, 14vw, 11rem);
  }

  /* Counter lebih rapat */
  .counter {
    margin-bottom: 2rem;
    font-size: 1.5rem;
  }

  /* Button naik sedikit agar tidak terlalu bawah */
  .button {
    bottom: 3rem;
    padding: 0.9rem 1.8rem;
    font-size: 0.95rem;
    margin-bottom: 7rem;
  }
}

/* ================= RESPONSIVE ================= */
@media (max-width: 768px) {
  .button {
    bottom: 2rem;
    padding: 0.75rem 1.5rem;
    font-size: 0.9rem;
  }

  .counter {
    margin-bottom: 1.5rem;
  }
}
</style>
