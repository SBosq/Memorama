<template>
  <fragment key="uniqueKey">
    <h1 class="titulo">Acuerdate Ojo!</h1>
    <transition-group tag="section" class="mesa-de-juego" name="shuffleTarj">
      <Tarjeta
          v-for="tarjeta in cardList"
          :key="`${tarjeta.value}-${tarjeta.variant}`"
          :matched="tarjeta.matched"
          :value="tarjeta.value"
          :visible="tarjeta.visible"
          :position="tarjeta.position"
          @estaTar="voltear"
      />
    </transition-group>
    <h2>{{ status }}</h2>
    <button @click="reempezar" class="otras">Otra Vez!</button>
  </fragment>
</template>

<script>
import Vue from 'vue';
import VueCompositionAPI from '@vue/composition-api';
import Tarjeta from "@/components/Tarjeta";
import Fragment from 'vue-fragment';
import _ from 'lodash'
import {ref, watch, computed} from '@vue/composition-api';

Vue.use(VueCompositionAPI)
Vue.use(Fragment.Plugin)

export default {
  name: 'App',
  components: {
    Tarjeta
  },
  setup() {
    const cardList = ref([])
    const usuarioSel = ref([])

    const status = computed(() => {
      if(paresRest.value === 0) {
        return 'Todo Encontrado!'
      } else {
        return `Pares Restantes: ${paresRest.value}`
      }
    })

    const paresRest = computed(() => {
      const carRest = cardList.value.filter(tarjeta => tarjeta.matched === false).length
      console.log(cardList.value)

      return carRest / 2
    })

    const reempezar = () => {
      cardList.value = _.shuffle(cardList.value)

      cardList.value = cardList.value.map((tarjeta, index) => {
        return {
          ...tarjeta,
          matched: false,
          position: index,
          visible: false
        }
      })
    }

    const tarjItems = ['Rick_1', 'PickleRick_2', 'Meseeks_3', 'Jessica_4', 'Summer_5', 'Birdperson_6',
                       'MrPoopy_7', 'Abradolf_Lincler_8', 'ScaryTerry_9', 'Blips&Chitz_10', 'Roy_11',
                       'K_Michael_12', 'Fart_13', 'NoobNoob_14', 'MortyJr_15', 'Jerry_16']

    tarjItems.forEach(item => {
      cardList.value.push({
        value: item,
        variant: 1,
        visible: false,
        position: null,
        matched: false
      })

      cardList.value.push({
        value: item,
        variant: 2,
        visible: false,
        position: null,
        matched: false
      })
    })

    cardList.value = cardList.value.map((tarjeta, index) => {
      return {
        ...tarjeta,
        position: index
      }
    })

    const voltear = payload => {
      cardList.value[payload.position].visible = true

      if (usuarioSel.value[0]) {
        if (
            usuarioSel.value[0].position === payload.position &&
                usuarioSel.value[0].faceValue === payload.faceValue
        ) {
          return
        } else {
          usuarioSel.value[1] = payload
        }
      }
      else {
        usuarioSel.value[0] = payload
      }
    }

    watch(usuarioSel, currentValue => {
      if (currentValue.length === 2) {
        const primera = currentValue[0]
        const segunda = currentValue[1]

        if (primera.faceValue === segunda.faceValue) {
          cardList.value[primera.position].matched = true
          cardList.value[segunda.position].matched = true
        } else {
          setTimeout(() => {
            cardList.value[primera.position].visible = false
            cardList.value[segunda.position].visible = false
          }, 1500)
        }

        usuarioSel.value.length = 0
      }
    },
        { deep:true}
    )

    return {
      voltear,
      cardList,
      usuarioSel,
      status,
      reempezar
    }
  }
}
</script>

<style>

html, body {
  margin: 0;
  padding: 0;
}

h1 {
  margin-top: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background-image: url('/public/images/bg-rm_500x500.png');
  background-color: #00070c;
  height: 100vh;
  color: #fff;
  padding-top: 60px;
}

.mesa-de-juego {
  display: grid;
  grid-template-columns: 225px 225px 225px 225px 225px 225px 225px 225px;
  grid-template-rows: 225px 225px 225px 225px;
  grid-column-gap: 30px;
  grid-row-gap: 30px;
  justify-content: center;
}

.titulo {
  text-align: center;
  font-size: 55px;
}

.otras {
  text-align: center;
  font-size: 35px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto;
}

.shuffleTarj-move {
  transition: transform 0.8s ease-in;
}
</style>