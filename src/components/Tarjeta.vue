<script>
import { computed } from '@vue/composition-api'
export default {
  props: {
    matched: {
      type: Boolean,
      default: false
    },
    position: {
      type: Number,
      required: true
    },
    value: {
      type: String,
      required: true
    },
    visible: {
      type: Boolean,
      default: false
    }
  },
  setup(props, context){
    const flippedStyles = computed(() => {
      if (props.visible) {
        return 'es-frente'
      }
    })
    const estaTar = () => {
      context.emit('estaTar', {
        position: props.position,
        faceValue: props.value
      })
    }

    return {
      flippedStyles,
      estaTar
    }
  }
}
</script>

<template>
  <div class="tarjeta" :class="flippedStyles" @click="estaTar">
    <div class="tarCara es-frente">
      <img :src="`@/public/images/${value}.png`" :alt="value" />
      <img v-if="matched" src="/public/images/checkmark.svg" alt="checkmark" class="icon-check"/>
    </div>
    <div class="tarCara es-atras">
      {{ position }}
    </div>
  </div>
</template>

<style>

.tarjeta {
  border: 5px solid #ccc;
  text-align: center;
  font-size: 25px;
  position: relative;
  transition: 0.5s transform ease-in;
  transform-style: preserve-3d;
}

.tarjeta.es-frente {
  transform: rotateY(180deg);
}

.tarCara {
  width: 100%;
  height: 100%;
  position: absolute;
  border-radius: 10px;
  backface-visibility: hidden;
}
.tarCara.es-frente {
  background-color: peachpuff;
  color: white;
  transform: rotateY(180deg);
}

.tarCara.es-atras {
  background-image: url('../../public/images/card-bg-empty_350x350.png');
  color: white;
}

.icon-check {
  position: absolute;
  right: 5px;
  bottom: 5px;
}
</style>