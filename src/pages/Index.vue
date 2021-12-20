<template>
  <q-page class="text-center flex column">
    <div id="exchange-input" class="q-py-md">
      <div class="row">
        <div class="col-6">
          <label>Dólar (USD)</label>
          <div class="input-group">
            <div class="input-group-prepend">
              <img
                alt="Bandera de Estados Unidos"
                src="~assets/usa-flag.png"
                style="width: 20px; height: 20px"
              >
            </div>
            <input v-model.number="USD" @change="changedUSD" type="number" class="form-control" min="0">
          </div>
        </div>
        <div class="col-6">
          <label>Bolívar (VES)</label>
          <div class="input-group">
            <div class="input-group-prepend">
              <img
                alt="Bandera de Venezuela"
                src="~assets/venezuela-flag.png"
                style="width: 20px; height: 20px"
              >
            </div>
            <input v-model.number="VES" @change="changedVES" type="number" class="form-control" min="0">
          </div>
        </div>
        <div class="col-12" id="exchange-rate">
          <span>1$ = {{ exchangeRate.toFixed(2) }} Bs</span>
        </div>
        <div class="col-12" id="add-button">
          <q-btn color="primary" icon="add" @click="addItem" class="btn">Agregar</q-btn>
        </div>
      </div>
    </div>
    <div id="exchange-history" class="q-py-sm" style="flex: 1 1 1px; overflow-y: auto; position: relative">
      <div class="items" v-if="items.length">
        <div class="row q-mt-md">
          <div class="col-6 col-title">Bs.</div>
          <div class="col-6 col-title">$.</div>
        </div>
        <div v-for="(item, index) in items" class="row" :key="index">
          <div class="col-6">{{ item.VES.toFixed(2) }}</div>
          <div class="col-6">{{ item.USD.toFixed(2) }}</div>
        </div>
      </div>
      <div class="no-items" v-else>
        <p>No has agregado ningún elemento.</p>
        <q-btn icon="visibility" color="primary" @click="opentuto = true">Ver tutorial</q-btn>
      </div>
      
    </div>
    <div id="exchange-total">
      <div class="row">
        <div class="col-6 col-totals">Bs. {{ totalVES.toFixed(2) }}</div>
        <div class="col-6 col-totals">$ {{ totalUSD.toFixed(2) }}</div>
      </div>
    </div>
    <q-dialog v-model="opentuto">
      <q-card>
        <q-card-section class="row items-center q-pb-none">
          <div class="text-h6">Al Cambio - Tutorial</div>
          <q-space />
          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>

        <q-card-section>
          <p class="q-mb-xs"><b>Calcular tasa de cambio</b></p>
          <p>Escribe un número en el campo de Dólar (USD) o Bolívar (USD) y se calculará automáticamente el valor en la otra moneda.</p>
          <p class="q-mb-xs"><b>Cambiar tasa de cambio</b></p>
          <p>Abre el menú y selecciona la opción "Tasa de cambio". Allí podrás seleccionar entre varias opciones. Desde sitios como DolarToday, BCV, EnParalelo, hasta tu propia tasa personalizada.</p>
        </q-card-section>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'PageIndex',
  setup() {
    return {
      opentuto: ref(false)
    } 
  },
  props: {
    exchangeRate: {
      type: Number,
      default: 0
    },
  },
  data() {
    return {
      items: [],
      VES: 0,
      USD: 0,
      exchangeHouse: 'dolartoday',
    }
  },
  
  methods: {
    addItem() {
      this.items.unshift({
        VES: this.VES,
        USD: this.USD
      })
    },
    changedVES() {
      this.USD = parseFloat((this.VES / this.exchangeRate).toFixed(2))
    },
    changedUSD() {
      this.VES = parseFloat((this.USD * this.exchangeRate).toFixed(2))
    }
  },
  computed: {
    totalVES: function() {
      let total = this.items.map(el => el.VES)
      return total.reduce((acc, val) => acc + val, 0);
    },
    totalUSD: function() {
      let total = this.items.map(el => el.USD)
      return total.reduce((acc, val) => acc + val, 0);
    }
  }
})
</script>