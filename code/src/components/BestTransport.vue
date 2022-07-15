<template>
<div>
  <div class="title">
    <b-navbar toggleable="lg" type="dark" variant="info">
    <div>
      <b-img :src="require('../assets/logo.png')" fluid class="logo" alt="Logo do Best Transport"></b-img>
    </div>
      <b-navbar-brand class="ml-2">
        <b>{{ appName }}</b>
      </b-navbar-brand>
    </b-navbar>
  </div>
  <main class="main-container">
    <h2>Como é o frete que você precisa?</h2>
    <hr class="main-title-decorator" size="2">
    <section class="inputs-container">
      <div>
        <p>destiny</p>
        <b-form-select v-model="selected" class="mb-3 destiny-select">
          <b-form-select-option :value="null">Selecione aqui o destino do frete</b-form-select-option>
          <b-form-select-option v-for="(entry, i) in selectList" v-bind:key="i" :value="entry">{{entry}}</b-form-select-option>
        </b-form-select>
        <p>Peso</p>
        <b-form-input v-model="text" class="weight-input" placeholder="Insira aqui o peso da carga em kg"></b-form-input>
      </div>
      <b-button variant="success" class="submit-btn" v-on:click="analisarEntradas">Analisar</b-button>
    </section>
    <section class="output-container">
      <h2>Estas são as melhores alternativas de frete que encontramos pra você: </h2>
      <hr class="main-title-decorator" size="2">
      <div>
        <b-jumbotron class="output-jumbotron jumbo-1">
          <b-img :src="require('../assets/moneybag.png')" fluid class="jumbo-icon" alt="Ícone de uma sacola de dinheiro"></b-img>
          <p>Frete mais barato: <span>{{cheapOption}}</span></p>
        </b-jumbotron>
      </div>
      <div>
        <b-jumbotron class="output-jumbotron jumbo-2">
        <b-img :src="require('../assets/clock.png')" fluid class="jumbo-icon" alt="Ícone de um relógio"></b-img>
          <p>Frete mais rápido: <span>{{fastOption}}</span></p>
        </b-jumbotron>
      </div>
    </section>
    <div class="no-display">
      {{fetchData}}
    </div>
  </main>
</div>
</template>

<script>
import {
  BNavbar,
  BNavbarBrand,
} from 'bootstrap-vue'

export default {
  components: {
    BNavbar,
    BNavbarBrand,
  },
  data() {
    const appName = '';
    const selectList = new Set();
    const fetchData = [];
    const cheapOption = '';
    const fastOption = '';

    return {
      appName,
      selected: null,
      fetchData,
      cheapOption,
      fastOption,
      selectList
    }
  },
  created() {
    this.axios.get('http://localhost:3000/transport')
      .then(response => {
        this.fetchData = response.data;
        this.fetchData.forEach((entry) => {
          this.selectList.add(entry.city);
        })
      });
    this.appName = 'Melhor frete'
  },
  methods: {
    // Implemente aqui os metodos utilizados na pagina
    analisarEntradas() {
      let destiny = document.querySelector(".destiny-select");
      let inputValue = document.querySelector(".weight-input");
      let heavy;
      inputValue.value > 100 ? heavy = true : heavy = false;
      let cheaper;
      let faster;

      const filtered = this.fetchData.filter((entry) => {
        return entry.city == destiny.value;
      });

      if(heavy) {
        let final = filtered.map((element) => {
          return element.cost_transport_heavy.split(' ')[1];
        });
        let min = Math.min(...final);
        cheaper = filtered.filter((entry) => {
          return entry.cost_transport_heavy.split(' ')[1] == min
        })[0];
      } else {
        let final = filtered.map((element) => {
          return element.cost_transport_light.split(' ')[1];
        });
        let min = Math.min(...final);
        cheaper = filtered.filter((entry) => {
          return entry.cost_transport_light.split(' ')[1] == min
        })[0];
      }

      this.cheapOption = `Transportadora ${cheaper.name} - R$${heavy? (cheaper.cost_transport_heavy.split(' ')[1] * inputValue.value).toFixed(2) : (cheaper.cost_transport_light.split(' ')[1] * inputValue.value).toFixed(2)} - ${cheaper.lead_time}`;

      let splitFaster = filtered.map(entry => entry.lead_time.split('h')[0]);
      let fewerHours = Math.min(...splitFaster);
      faster = filtered.filter((entry) => {
        return entry.lead_time.split('h')[0] == fewerHours;
      })[0];
      this.fastOption = `Transportadora ${faster.name} - R$${heavy? (faster.cost_transport_heavy.split(' ')[1] * inputValue.value).toFixed(2) : (faster.cost_transport_light.split(' ')[1] * inputValue.value).toFixed(2)} - ${faster.lead_time}`;
    },
  }
}
</script>

<style scoped>
  .logo {
    width:3rem;
    height:3rem;
    margin-left: 2rem;
    filter: invert(100%);
  }

  .title {
    border: 1px solid #000000;
    margin: 2% 2% 0 2%;
  }

  .title .navbar {
    background-color: #00aca6 !important;
  }

  .title .navbar-brand {
    font-size: 2rem;
    margin-left: 20px;
    text-transform: uppercase;
  }

  .main-container {
    margin: 0 2% 2% 2%;
    border: 1px solid #000000;
    padding: 2%;
    border-top: none;
  }

  h2 {
    margin-left: 1rem;
  }

  .main-title-decorator {
    width: 50%
  }

  .inputs-container {
    margin: 2%;
  }

  .inputs-container p {
    font-size: 1.3rem;
    margin: 0;
  }

  .destiny-select {
    border: 1.5px solid #000000;
    background-color: #c3d0e6;
    border-radius: 0;
    width: 50%;
    height: 2.5rem;
    margin-bottom: 2rem !important;
  }

  .weight-input {
    border: 1.5px solid #000000;
    background-color: #c3d0e6;
    border-radius: 0;
    width: 50%;
    height: 2.5rem;
    margin-bottom: 1.5rem; 
  }

  .weight-input::placeholder {
    color: #000000;
  }

  .submit-btn {
    border: 1px solid #000000;
    color: #000000;
    margin-left: 15%;
    font-weight: 700;
    min-width: 15%;
  }

  .output-jumbotron {
    display: flex;
    align-items: center;
    margin-bottom: 1rem;
    padding: .5rem;
    height: 4rem;
    border-radius: 15px;
  }

  .output-jumbotron p {
    margin: 0;
  }

  .jumbo-icon {
    padding: .5rem;
    width:3rem;
    height:3rem;
  }

  .jumbo-1 {
    border: 2px dashed #328a21;
    background-color: #acd4e8;
  }

  .jumbo-2 {
    border: 2px dashed #426cbd;
    background-color: #9dc0d1;
  }

  .no-display {
    display:none;
  }

  p span {
    font-weight: 700;
  }
</style>
