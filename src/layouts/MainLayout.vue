<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />
        <q-toolbar-title> Al Cambio </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
      <q-list>
        <q-item-label header> Menú </q-item-label>
        <q-dialog v-model="exchangeRateModalOpen" persistent>
          <q-card style="min-width: 350px">
            <q-card-section>
              <div class="text-h6">Tasa de cambio</div>
            </q-card-section>

            <q-card-section class="q-pt-none">
              <q-option-group
                v-model="exchangeHouse"
                :options="options"
                color="primary"
              />
              <q-input class="q-px-md" v-if="exchangeHouse=='custom_rate'"
                dense
                v-model.number="myRate"
                autofocus
                type="number"
                :min="0"
                :rules="[val => val !== '' && !isNaN(val) || 'Por favor, escribe una tasa válida']"
              />
            </q-card-section>

            <q-card-actions align="right" class="text-primary">
              <q-btn flat label="Cancelar" v-close-popup />
              <q-btn flat label="Actualizar tasa" @click="updateExchangeRate"/>
            </q-card-actions>
          </q-card>
        </q-dialog>

        <q-item clickable tag="a" href="#" @click="exchangeRateModalOpen = true">
          <q-item-section avatar>
            <q-icon name="currency_exchange" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Tasa de cambio</q-item-label>
          </q-item-section>
        </q-item>

      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view :exchangeRate="exchangeRate"/>
    </q-page-container>
  </q-layout>
</template>

<script>

import { useQuasar } from 'quasar'
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "MainLayout",

  data() {
    return {
      exchangeHouse: 'dolar_today',
      exchangeRate: 0,
      myRate: 0,
    }
  },

  methods: {
    updateExchangeRate() {
      if (!isNaN(this.myRate)) {
        this.$q.notify({
          message: 'Tasa actualizada.',
          icon: 'announcement',
          actions: [ { label: 'OK', color: 'white' } ]
        });
        this.exchangeRate = parseFloat(this.myRate.toFixed(2));
      } else {
        this.myRate = 0;
      }
    }
  },

  created() {
    fetch('https://s3.amazonaws.com/dolartoday/data.json')
      .then(response => response.json())
      .then(data => this.exchangeRate = data.USD.dolartoday)      
  },

  setup() {
    const $q = useQuasar();
    const leftDrawerOpen = ref(false);
    const exchangeRateModalOpen = ref(false);

    return {
      leftDrawerOpen,
      exchangeRateModalOpen,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
      options: [
        {
          label: 'Dolar Today',
          value: 'dolar_today'
        },
        /*{
          label: 'Banco Central de Venezuela',
          value: 'bcv'
        },
        {
          label: 'EnParalelo',
          value: 'en_paralelo'
        },
        */
        {
          label: 'Personalizado',
          value: 'custom_rate'
        },
      ]
    };
  },
});
</script>
