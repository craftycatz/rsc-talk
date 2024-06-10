<template>
  <div class="w-full h-full">
    <Header />
    <div v-if="loading" class="flex justify-center items-center h-full">
      <Spinner />
    </div>
    <div v-else class="flex justify-center items-center h-full">
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
    };
  },
  mounted() {
    setTimeout(() => {
      this.loading = false;
    }, 4000); // 4 seconds
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
