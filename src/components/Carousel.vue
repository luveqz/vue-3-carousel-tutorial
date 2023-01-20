<template>
  <div class="carousel">
    <div class="inner" ref="inner" :style="innerStyles">
      <div class="card" v-for="card in cards" :key="card">
        {{ card }}
      </div>
    </div>
  </div>
  <button @click="prev">prev</button>
  <button @click="next">next</button>
</template>

<script>
import {ref,onMounted} from '@vue/runtime-core'

export default {
  name: "Carousel",
  setup() {
    const cards = ref([1, 2, 3, 4, 5, 6, 7, 8])
    const innerStyles = ref({})
    const step = ref('')
    const transitioning = ref(false)
    const inner = ref(null)

    function setStep () {
      const innerWidth = inner.value.scrollWidth
      const totalCards = cards.value.length
      step.value = `${innerWidth / totalCards}px`
    }

    function next () {
      if (transitioning.value) return
      transitioning.value = true
      moveLeft()
      afterTransition(() => {
        const card = cards.value.shift()
        cards.value.push(card)
        resetTranslate()
        transitioning.value = false
      })
    }

    function prev () {
      if (transitioning.value) return
      transitioning.value = true
      moveRight()
      afterTransition(() => {
        const card = cards.value.pop()
        cards.value.unshift(card)
        resetTranslate()
        transitioning.value = false
      })
    }

    function moveLeft () {
      innerStyles.value = {
        transform: `translateX(-${step.value})
                    translateX(-${step.value})`
      }
    }

    function moveRight () {
      innerStyles.value = {
        transform: `translateX(${step.value})
                    translateX(-${step.value})`
      }
    }

    function afterTransition (callback) {
      const listener = () => {
        callback()
        inner.value.removeEventListener('transitionend', listener)
      }
      inner.value.addEventListener('transitionend', listener)
    }

    function resetTranslate () {
      innerStyles.value = {
        transition: 'none',
        transform: `translateX(-${step.value})`
      }
    }

    onMounted(() => {
      setStep()
      resetTranslate()
    });

    return {
      cards,
      next,
      prev,
      innerStyles,
      inner,
    }
  }
}
</script>

<style scoped>
.carousel {
  width: 170px;
  overflow: hidden;
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
  background-color: #39b1bd;
  color: white;
  border-radius: 4px;
  align-items: center;
  justify-content: center;
}
/* optional */
button {
  margin-right: 5px;
  margin-top: 10px;
}
</style>
