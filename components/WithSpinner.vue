<template>
  <div class="w-full h-full">
    <div v-if="!firstPaint"></div>
    <div
      v-else-if="firstPaint && loading"
      class="flex justify-center items-center h-full"
    >
      <Header />
      <Spinner />
    </div>
    <div v-else class="flex justify-center items-center h-full">
      <Header />
      <Card v-for="n in 4" :key="n" :product-name="n" class="m-2" />
    </div>
  </div>
</template>

<script>
import Header from "./Header.vue";
import Spinner from "./Spinner.vue";
import Card from "./Card.vue";

export default {
  name: "MainComponent",
  components: {
    Header,
    Spinner,
    Card,
  },
  data() {
    return {
      loading: true,
      firstPaint: false,
    };
  },
  mounted() {
    setTimeout(() => {
      this.firstPaint = true;
    }, 4000);

    setTimeout(() => {
      this.loading = false;
    }, 8000); // 4 seconds
  },
};
</script>

<style scoped>
.flex {
  display: flex;
}
.justify-center {
  justify-content: center;
}
.items-center {
  align-items: center;
}
.h-full {
  height: 100%;
}
.m-2 {
  margin: 0.5rem;
}
</style>
