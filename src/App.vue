<template>
  <div class="container">
    <div class="hero">
      <!-- <video class="responsive-video" autoplay loop playsinline preload="auto">
        <source type="video/webm"
          src="https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/homepage/dota_montage_webm.webm">
        <source type="video/mp4"
          src="https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/homepage/dota_montage_02.mp4">
      </video> -->
      <div class="video-overlay">
        <p class="overlay-text">"Dota Points <br>Calculator"</p>
        <div class="line"></div>
      </div>
      <div class="bottom-fade"></div>
      <div class="card-container">
        <Card class="main-card">
          <template #content>
            <div class="main-content">
              <div class="input-container">
                <label for="abates">Abates:</label>
                <InputNumber v-model="abates" inputId="abates" class="custom-input"
                  inputStyle="width: 40px; text-align: center; padding: 0; margin-left: 10px" />
              </div>
              <div class="input-container">
                <label for="assistencias">Assistências:</label>
                <InputNumber v-model="assistencias" inputId="assistencias" class="custom-input"
                  inputStyle="width: 40px; text-align: center; padding: 0; margin-left: 10px" />
              </div>
              <div class="input-container">
                <label for="farm">Farm (em ouro):</label>
                <InputNumber v-model="farm" inputId="farm" class="custom-input"
                  inputStyle="width: 100px; text-align: center; padding: 0; margin-left: 10px" />
              </div>
              <div class="input-container">
                <label for="torresDerrubadas">Torres Derrubadas:</label>
                <InputNumber v-model="torresDerrubadas" inputId="torresDerrubadas" class="custom-input"
                  inputStyle="width: 40px; text-align: center; padding: 0; margin-left: 10px" />
              </div>
              <div class="input-container">
                <label for="roshanMorto">Roshan Morto:</label>
                <InputNumber v-model="roshanMorto" inputId="roshanMorto" class="custom-input"
                  inputStyle="width: 40px; text-align: center; padding: 0; margin-left: 10px" />
              </div>
              <div class="input-container">
                <label for="tempoSobrevividoMinutos">Tempo Sobrevivido (minutos):</label>
                <InputNumber v-model="tempoSobrevividoMinutos" inputId="tempoSobrevividoMinutos" class="custom-input"
                  inputStyle="width: 40px; text-align: center; padding: 0; margin-left: 10px" />
              </div>
              <div class="input-sup">
                <label for="suporte">É Suporte?</label>
                <Checkbox v-model="suporte" :binary="true" />
              </div>

              <h1>Pontuação Final: {{ pontuacaoFinal }}</h1>
              <div class="erro-msg">{{ errorMessage }}</div>
            </div>
          </template>
        </Card>
      </div>
    </div>
  </div>
  <div class="footer-container" :class="{ 'hidden': scrolledDown }">
    <div class="config-container">
      <div class="find-game-id">
        <h3>Game ID: <InputNumber v-model="gameId" inputStyle="width: 150px; height:20px"></InputNumber>
        </h3>
        <Button type="button" label="Buscar" :loading="loading" @click="searchGame" />
      </div>
      <h3>Configuração dos Pesos das Pontuações</h3>
      <div class="config-input-row">
        <div class="config-input">
          <label for="abatePontos">Abates:</label>
          <InputNumber v-model="abatePontos" inputId="abatePontos" class="custom-input"
            inputStyle="width: 50px; text-align: center; padding: 0; margin-left: 10px" />
        </div>
        <div class="config-input">
          <label for="assistenciaPontos">Assistências:</label>
          <InputNumber v-model="assistenciaPontos" inputId="assistenciaPontos" class="custom-input"
            inputStyle="width: 50px; text-align: center; padding: 0; margin-left: 10px" />
        </div>
        <div class="config-input">
          <label for="farmPontosPor1000">Farm (Ouro por 1000):</label>
          <InputNumber v-model="farmPontosPor1000" inputId="farmPontosPor1000" class="custom-input"
            inputStyle="width: 70px; text-align: center; padding: 0; margin-left: 10px" />
        </div>
        <div class="config-input">
          <label for="objetivoPontosPorTorre">Torres Derrubadas:</label>
          <InputNumber v-model="objetivoPontosPorTorre" inputId="objetivoPontosPorTorre" class="custom-input"
            inputStyle="width: 70px; text-align: center; padding: 0; margin-left: 10px" />
        </div>
        <div class="config-input">
          <label for="objetivoPontosPorRoshan">Roshan Morto:</label>
          <InputNumber v-model="objetivoPontosPorRoshan" inputId="objetivoPontosPorRoshan" class="custom-input"
            inputStyle="width: 70px; text-align: center; padding: 0; margin-left: 10px" />
        </div>
        <div class="config-input">
          <label for="sobrevivenciaPontosPorMinuto">Tempo Sobrevivido (minutos):</label>
          <InputNumber v-model="sobrevivenciaPontosPorMinuto" inputId="sobrevivenciaPontosPorMinuto"
            class="custom-input" inputStyle="width: 70px; text-align: center; padding: 0; margin-left: 10px" />
        </div>
      </div>
    </div>
  </div>
  <Card class="card-section">
    <template #content>
      <div class="card-container-data">
        <Card class="card-player" v-for="(playerData, index) in playersData" :key="playerData.account_id"
          :style="{ backgroundImage: `url(${getHeroBackground(playerData.hero_id)})`, backgroundSize: 'cover' }">
          <template #title>
            <div class="hero-name">{{ getHeroName(playerData.hero_id) }}</div>
          </template>
          <!-- Substituir o ID do herói pelo nome correspondente -->
          <!-- <template #subtitle>{{ getHeroName(playerData.hero_id) }}</template> -->
          <template #subtitle v-if="playerData.personaname">
            <div class="player-name"> {{ playerData.personaname }}</div>
          </template>
          <template #subtitle v-else>
            <div class="player-name">Nome não encontrado</div>
          </template>
          <template #content>
            <div class="heroes">
              <div class="player-medal">
                <img :src="getPlayerMedalImage(playerData.rank_tier)" :alt="getPlayerMedalAlt(playerData.rank_tier)">
              </div>
              <div>{{ playerData.net_worth }}g Net Worth</div>
              <div>{{ playerData.kills }} Abates</div>
              <div>{{ playerData.deaths }} Mortes</div>
              <div>{{ playerData.assists }} Assistências</div>
              <div>{{ playerData.last_hits }} Last Hits</div>
            </div>
          </template>
          <template #footer>
            <div class="pontuacao">Pontuação Final: {{ playersTotalScore[index] }}</div>
          </template>
        </Card>
      </div>
    </template>
  </Card>

</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import Card from 'primevue/card';
import InputNumber from 'primevue/inputnumber';
import Checkbox from 'primevue/checkbox';
import Button from 'primevue/button';
import axios from 'axios';

import heroesData from '../src/herodataComplete.js';
import ranksData from '../src/ranksdata.js';

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

const playersData = ref([]);
const gameId = ref();
const loading = ref(false);
const errorMessage = ref('');

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

// Use a função `computed` para calcular a pontuação total de cada jogador
const playersTotalScore = computed(() => {
  return playersData.value.map(playerData => {
    // Calcula a pontuação total de cada jogador somando kills, deaths, assists e last hits
    return [(playerData.kills * abatePontos.value) + (Math.floor(playerData.net_worth / 1000) * farmPontosPor1000.value)  + (playerData.assists * assistenciaPontos.value)] - playerData.deaths;
  });
});

function getHeroName(id) {
  const heroId = id.toString(); // Converter o ID para uma string
  const hero = heroesData[heroId];
  return hero ? hero.localized_name : 'Herói não encontrado';
}

const scrolledDown = ref(false);

window.addEventListener('scroll', () => {
  scrolledDown.value = window.scrollY > 100;
});

function searchGame() {
  loading.value = true;
  axios.get(`https://api.opendota.com/api/matches/${gameId.value}`)
    .then(response => {
      playersData.value = response.data.players;
      console.log(playersData.value);
    })
    .catch(error => {
      console.error('Ocorreu um erro ao fazer a requisição:', error);
      errorMessage.value = 'Não foi possível encontrar o jogo. Por favor, verifique o ID do jogo e tente novamente.';
      console.log(errorMessage.value)
      playersData.value = [];
    });
  // Desça 500px abaixo
  // Atraso de 2 segundos
  setTimeout(() => {
    loading.value = false;
    // Desça 500px abaixo
    window.scrollTo({
      top: window.scrollY + 470,
      behavior: 'smooth'
    });
  }, 2000); // 2000 milissegundos = 2 segundos
}

function getHeroBackground(heroId) {
  const hero = heroesData[heroId];
  return hero ? hero.url_vertical_portrait : ''; // Retorna a URL da imagem do herói se disponível, senão retorna uma string vazia
}

function getPlayerMedalImage(rankNumber) {
  // Obtém o objeto de rank correto com base no número retornado pela API
  const rankData = ranksData[rankNumber];
  // Verifica se o rankData foi encontrado
  if (rankData) {
    // Verifica se o rankNumber é maior ou igual a 80
    if (rankNumber >= 80) {
      // Retorna o caminho da imagem para ranks maiores ou iguais a 80
      return "/src/assets/images/imortal0.png";
    } else {
      // Retorna o caminho da imagem do rank padrão
      return rankData.image;
    }
  } else {
    // Retorna um caminho de imagem padrão ou uma string vazia em caso de erro
    return "/src/assets/images/uncalibrated.png";
  }
}

function getPlayerMedalAlt(rankNumber) {
  // Obtém o objeto de rank correto com base no número retornado pela API
  const rankData = ranksData[rankNumber];
  // Retorna o nome do rank ou uma string vazia em caso de erro
  return rankData ? rankData.name : "";
}

</script>

<style>
.p-card-title,
.p-card-footer {
  text-align: center;
}
</style>

<style scoped>
.find-game-id {
  margin-top: -70px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
}

.find-game-id Button {
  height: 20px;

}

.author {
  position: fixed;
  top: -10px;
  /* Distância do fundo */
  left: 50%;
  transform: translateX(-50%);
  text-align: center;
  width: 100%;
  color: white;
}

.footer-container {
  position: fixed;
  /* Posição fixa */
  bottom: 0;
  /* Alinha no final da página */
  width: 100%;
  /* Largura total */
  background-color: rgba(0, 0, 0, 0.5);
  /* Cor de fundo */
  color: white;
  /* Cor do texto */
  padding: 20px;
  /* Espaçamento interno */
  text-align: center;
  /* Alinha o texto ao centro */
  z-index: 999;
  /* Coloca na frente de todos os outros elementos */
  background-color: black;
}

.config-container {
  margin-top: 40px;
  /* Espaçamento superior */
}

.main-content {
  text-align: center;
}

.config-input-row {
  display: flex;
  /* Exibe os inputs na horizontal */
  justify-content: center;
  /* Centraliza os inputs horizontalmente */
}

.config-input {
  margin: 0 10px;
  /* Espaçamento entre os inputs */
}

.custom-input {
  margin-bottom: 5px;
}

.container {
  position: relative;
}

.hero {
  position: relative;
  width: 100%;
  height: 75vh;
  margin-bottom: -90px;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  background-image: url('https://cdn.cloudflare.steamstatic.com/apps/dota2/images/dota_react/home/radiant_dire5.jpg');
  background-size: cover;
  /* Para cobrir todo o espaço da div */
  background-position: center;
  /* Centralizar a imagem */

}

.responsive-video {
  width: 100%;
  height: auto;
}

.video-overlay {
  position: absolute;
  top: 100px;
  left: 200px;
  transform: translate(-50%, -50%);
  z-index: 1;
}

.overlay-text {
  font-family: 'Nexa-Heavy';
  color: white;
  font-size: 45px;
  text-align: left;
  margin-left: 60px;
  text-transform: uppercase;
  line-height: 43px;
}

.bottom-fade {
  width: 100%;
  height: 200px;
  position: absolute;
  bottom: 0px;
  background: -webkit-gradient(linear, left top, left bottom, color-stop(60%, rgba(0, 0, 0, 0)), color-stop(80%, rgba(0, 0, 0, 0.7)), color-stop(100%, black));
}

.card-container {
  position: absolute;
  z-index: 89;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.main-card {
  background-color: rgba(0, 0, 0, 0.5);
  color: white;
  width: 450px;
  padding: 20px;
}

.line {
  width: 150px;
  height: 2px;
  background-color: orangered;
  margin-left: 65px;
  margin-top: -45px;
}

@font-face {
  font-family: 'Nexa-light';
  src: url('/src/assets/fonts/Nexa-ExtraLight.ttf') format('truetype');
  font-weight: normal;
  font-style: normal;
}

@font-face {
  font-family: 'Nexa-heavy';
  src: url('src/assets/fonts/Nexa-Heavy.ttf') format('truetype');
  font-weight: bold;
  font-style: normal;
}

.card-section {
  padding: 20px;
  margin-top: 50px;
  margin-bottom: 200px;
  background-color: black;
}

.card-container-data {
  margin: 0 auto;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 10px;
}

.card-player {
  position: relative;
  display: flex;
  flex-wrap: wrap;
  width: 250px;
}

.player-name {
  font-size: 14px;
  height: 20px;
  width: 210px;
  text-align: center;
  color: #fff;
  font-weight: bold;
  background-color: rgba(0, 0, 0, 0.527);
  padding-top: 1px;
}

.player-name span {
  height: 30px;
}

.player-medal {
  position: absolute;
  bottom: 13px;
  left: 5px;
}

.player-medal img {
  width: 60px;
}

.hidden {
  display: none;
}

.erro-msg {
  margin-top: 50px;
}

.heroes div,
.hero-name,
.pontuacao {
  color: #fff;
  font-weight: bold;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
  /* Adicione uma sombra ao texto */
}


.pontuacao {
  color: #fff;
  font-weight: bold;
  background-color: rgba(0, 0, 0, 0.527);
  width: 150px;
  margin-left: 55px;
}
</style>
