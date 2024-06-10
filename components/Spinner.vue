<template>
  <div role="status" class="spinner">
    <svg
      aria-hidden="true"
      class="spinner-icon"
      viewBox="0 0 100 100"
      fill="currentColor"
    >
      <circle
        class="path"
        cx="50"
        cy="50"
        r="45"
        stroke="currentColor"
        stroke-width="10"
        fill="none"
      />
    </svg>
    <span class="sr-only">Loading...</span>
  </div>
</template>

<script>
export default {
  name: "Spinner",
};
</script>

<style scoped>
.spinner {
  display: inline-block;
  width: 50px;
  height: 50px;
}
.spinner-icon {
  animation: spin 1s linear infinite;
}
.path {
  stroke-dasharray: 283;
  stroke-dashoffset: 75;
  stroke-linecap: round;
}
@keyframes spin {
  100% {
    transform: rotate(360deg);
  }
}
</style>
