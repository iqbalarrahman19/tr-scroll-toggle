<template>
  <div>
    <Header
      :steps="steps"
      :activeStep="activeStep"
      :stepProgress="stepProgress" />

    <SectionOne @scroll-progress="onScrollProgress" />
    <SectionTwo @scroll-progress="onScrollProgress" />
    <Thankyou />
  </div>
</template>

<script>
import Header from "@/components/header.vue";
import SectionOne from "@/components/section-1.vue";
import SectionTwo from "@/components/section-2.vue";
import Thankyou from "@/components/thankyou.vue";

export default {
  components: { Header, SectionOne, SectionTwo, Thankyou },

  data() {
    return {
      activeStep: 0,
      stepProgress: 0,
      steps: ["plan", "design", "build"],
    };
  },

  methods: {
    onScrollProgress({ activeStep, stepProgress }) {
      this.activeStep = activeStep;
      this.stepProgress = stepProgress;
    },
  },
};
</script>
