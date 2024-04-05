<template>
  <Card class="main-card">
    <template #content>
      <div class="main-content">
        <div class="input-container">
          <label for="abates">Abates:</label>
          <InputNumber v-model="abates" inputId="abates" class="custom-input"/>
        </div>
        <div class="input-container">
          <label for="assistencias">Assistências:</label>
          <InputNumber v-model="assistencias" inputId="assistencias" class="custom-input"/>
        </div>
        <div class="input-container">
          <label for="farm">Farm (em ouro):</label>
          <InputNumber v-model="farm" inputId="farm" class="custom-input"/>
        </div>
        <div class="input-container">
          <label for="torresDerrubadas">Torres Derrubadas:</label>
          <InputNumber v-model="torresDerrubadas" inputId="torresDerrubadas" class="custom-input"/>
        </div>
        <div class="input-container">
          <label for="roshanMorto">Roshan Morto:</label>
          <InputNumber v-model="roshanMorto" inputId="roshanMorto" class="custom-input"/>
        </div>
        <div class="input-container">
          <label for="tempoSobrevividoMinutos">Tempo Sobrevivido (minutos):</label>
          <InputNumber v-model="tempoSobrevividoMinutos" inputId="tempoSobrevividoMinutos" class="custom-input"/>
        </div>
        <div class="input-sup">
          <label for="suporte">É Suporte?</label>
          <Checkbox v-model="suporte" :binary="true" />
        </div>
        <h1>Pontuação Final: {{ pontuacaoFinal }}</h1>
      </div>
    </template>
  </Card>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import Card from 'primevue/card';
import InputNumber from 'primevue/inputnumber';
import Checkbox from 'primevue/checkbox';

const abatePontos = ref(2);
const assistenciaPontos = ref(1);
const farmPontosPor1000 = ref(1);
const objetivoPontosPorTorre = ref(5);
const objetivoPontosPorRoshan = ref(10);
const sobrevivenciaPontosPorMinuto = ref(1);

const abates = ref(0);
const assistencias = ref(0);
const farm = ref(0);
const torresDerrubadas = ref(0);
const roshanMorto = ref(0);
const tempoSobrevividoMinutos = ref(0);
const suporte = ref(false);

const pontuacaoFinal = computed(() => {
  let pontuacao = 0;

  // Pontuação por abates
  pontuacao += abates.value * abatePontos.value;

  // Pontuação por assistências
  pontuacao += assistencias.value * assistenciaPontos.value;

  // Pontuação por farm
  pontuacao += Math.floor(farm.value / 1000) * farmPontosPor1000.value;

  // Pontuação por objetivos
  pontuacao += torresDerrubadas.value * objetivoPontosPorTorre.value;
  pontuacao += roshanMorto.value * objetivoPontosPorRoshan.value;

  // Pontuação por sobrevivência
  pontuacao += tempoSobrevividoMinutos.value * sobrevivenciaPontosPorMinuto.value;

  // Pontuação extra para suportes que fizeram abates
  if (suporte.value && abates.value > 0) {
    pontuacao += 1;
  }

  return pontuacao;
});
</script>

<style scoped>
.main-card {
  width: 700px;
  height: 750px;
  margin: 0 auto;
  background-image: url('https://i.pinimg.com/originals/4b/90/f6/4b90f6f36bbd11daf3fccbb4c879c4ca.png'); /* URL da imagem do Roshan */
  background-size: cover;
  background-position: center;
}

.main-content{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.main-content h1{
  font-weight: bold;
  color: #fff;
}

.input-container, .input-sup {
  margin-bottom: 1rem;
  font-weight: bold;
  color: #fff;
}

.input-container label {
  display: block;
  margin-bottom: 0.5rem;
}

.custom-input {
  width: 100%;
  text-align: left;
}
</style>
