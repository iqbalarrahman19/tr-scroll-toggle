<template>
  <section class="thank-you">
    <h1 class="title">Thank you</h1>
  </section>
</template>

<script>
export default {
  name: "ThankYou",
};
</script>

<style scoped>
.thank-you {
  width: 100%;
  height: 100vh;
  background: radial-gradient(circle at center, #0f1606 0%, #0b1205 70%);
  display: flex;
  align-items: center;
  justify-content: center;
}

/* TEXT */
.title {
  font-family: "Playfair Display", serif;
  font-style: italic;
  font-weight: 400;
  font-size: clamp(3rem, 8vw, 6rem);
  color: #c8ff5c;
  letter-spacing: 0.02em;
}
</style>
