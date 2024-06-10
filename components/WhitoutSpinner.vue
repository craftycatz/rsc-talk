<template>
  <div class="w-full h-full">
    <div v-if="!loaded" class="h-full"></div>
    <div v-else class="flex justify-center items-center h-full">
      <Header />
      <div class="flex justify-center items-center">
        <Card v-for="n in 4" :key="n" :product-name="n" class="m-2" />
      </div>
    </div>
  </div>
</template>

<script>
import Header from "./Header.vue";
import Card from "./Card.vue";

export default {
  name: "MainComponent",
  components: {
    Header,
    Card,
  },
  data() {
    return {
      loaded: false,
    };
  },
  mounted() {
    setTimeout(() => {
      this.loaded = true;
    }, 6000); // 4 seconds
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
