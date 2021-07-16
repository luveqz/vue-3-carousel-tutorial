<template>
  <div class="carousel">
    <div class="inner">
      <div class="card" v-for="card in cards" :key="card">
        {{ card }}
      </div>
    </div>
  </div>
  <button>prev</button>
  <button>next</button>
</template>

<script>
export default {
  data () {
    return {
      cards: [1, 2, 3, 4, 5, 6, 7, 8]
    }
  }
}
</script>

<style>
.carousel {
  width: 170px;
  overflow: hidden;

  /* optional */
  margin-bottom: 10px;
}

.inner {
  transition: transform 0.2s;
  white-space: nowrap;
}

.card {
  width: 40px;
  margin-right: 10px;
  display: inline-flex;

  /* optional */
  height: 40px;
  border-radius: 4px;
  background-color: #b0b0b0;
  align-items: center;
  justify-content: center;
}
</style>
