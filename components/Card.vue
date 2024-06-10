<template>
  <div
    class="max-w-30 p-6 bg-white border border-gray-200 rounded-lg shadow dark:bg-gray-800 dark:border-gray-700 text-sm"
  >
    <a href="#">
      <h5
        class="mb-2 text-lg font-bold tracking-tight text-gray-900 dark:text-white"
      >
        {{ productName }}
      </h5>
    </a>
    <p class="mb-3 font-normal text-gray-700 dark:text-gray-400">
      Description for Product lorem ipsum dolor et {{ description }}
    </p>
    <a
      href="#"
      class="inline-flex text-sm items-center px-3 py-2 text-sm font-medium text-center text-white bg-blue-700 rounded-lg hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
    >
      Add to Cart
    </a>
  </div>
</template>

<script>
export default {
  name: "ProductCard",
  props: {
    productName: {
      type: String,
      required: true,
    },
    description: {
      type: String,
      required: true,
    },
  },
};
</script>

<style scoped>
/* Add any additional styles if necessary */
</style>
