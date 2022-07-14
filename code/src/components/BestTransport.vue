<template>
<div>
  <div class="title">
    <b-navbar toggleable="lg" type="dark" variant="info">
    <div>
      <b-img :src="require('../assets/logo.png')" fluid class="logo" alt="Responsive image"></b-img>
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
        <p>Destino</p>
        <b-form-select v-model="selected" class="mb-3 destiny-select">
          <b-form-select-option :value="null">Selecione aqui o destino do frete</b-form-select-option>
          <b-form-select-option v-bind:key="entry.city" v-for="entry in fetchData">{{entry.city}}</b-form-select-option>
        </b-form-select>
        <p>Peso</p>
        <b-form-input v-model="text" class="weight-input" placeholder="Insira aqui o peso da carga em kg"></b-form-input>
      </div>
      <b-button variant="success" class="submit-btn">Analisar</b-button>
    </section>
    <section>
    </section>
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
    const fetchData = '';

    return {
      appName,
      selected: null,
      fetchData
    }
  },
  created() {
    this.axios.get('http://localhost:3000/transport')
      .then(response => {
        this.fetchData = response.data;
      });
    this.appName = 'Melhor frete'
  },
  methods: {
    // Implemente aqui os metodos utilizados na pagina
    analisarEntradas() {
      
    },
  },
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

  .main-container h2 {
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
</style>
