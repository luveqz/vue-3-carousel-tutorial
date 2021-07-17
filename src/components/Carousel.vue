<template>
  <div class="carousel">
    <div class="inner" ref="inner" :style="innerStyles">
      <div class="card" v-for="card in cards" :key="card">
        {{ card }}
      </div>
    </div>
  </div>
  <button>prev</button>
  <button @click="next">next</button>
</template>

<script>
export default {
  data () {
    return {
      cards: [1, 2, 3, 4, 5, 6, 7, 8],
      innerStyles: {},
      step: ''
    }
  },

  mounted () {
    this.setStep()
  },

  methods: {
    setStep () {
      const innerWidth = this.$refs.inner.scrollWidth
      const totalCards = this.cards.length
      this.step = `${innerWidth / totalCards}px`
    },

    next () {
      this.moveLeft()

      this.afterTransition(() => {
        const card = this.cards.shift()
        this.cards.push(card)
        this.resetTranslate()
      })
    },

    moveLeft () {
      this.innerStyles = {
        transform: `translateX(-${this.step})`
      }
    },

    afterTransition (callback) {
      const listener = () => {
        callback()
        this.$refs.inner.removeEventListener('transitionend', listener)
      }
      this.$refs.inner.addEventListener('transitionend', listener)
    },

    resetTranslate () {
      this.innerStyles = {
        transition: 'none',
        transform: 'translateX(0)'
      }
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
