<template>
  <div>
    <SectionOne />
    <SectionTwo />
    <Thankyou />
  </div>
</template>

<script>
import SectionOne from "@/components/section-1.vue";
import SectionTwo from "@/components/section-2.vue";
import Thankyou from "@/components/thankyou.vue";

export default {
  components: { SectionOne, SectionTwo, Thankyou },
};
</script>
