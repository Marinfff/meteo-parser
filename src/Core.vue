<script>
  import request from 'request';
  import cheerio from 'cheerio';
  import {ipcRenderer } from 'electron'

  export default {
    name: 'Core',
    data() {
      return {
        meteo: '',
        updateTime: 0,
        cities: [
          {
            text: 'Chisinau',
            value: 'Chisinau/Chisinau/'
          },
          {
            text: 'Balti',
            value: 'Balti/Balti/'
          },
          {
            text: 'Floresti',
            value: 'Floresti/Floresti/'
          },
        ],
        city: 'Balti/Balti/',
        timer: null,
        timeouts: [0, 1, 2, 3, 4, 5, 10],
        url: 'https://www.meteo2.md/ro/Prognoza_Meteo_10zile/'
      };
    },
    computed: {
      html() {
        if (this.meteo) {
          this.meteo('.inside_adds_fcast')
            .remove();
          return this.meteo('#Meteo')
            .html();
        }
        return '';
      }
    },
    mounted() {
      this.loadData();
    },
    methods: {
      setTimer() {
        if (this.updateTime) {
          this.timer && clearTimeout(this.timer);
          this.timer = setTimeout(() => {
            this.loadData();
          }, this.updateTime * 1000);
        }
      },
      loadData() {
        request(this.url + this.city, (error, response, body) => {
          if (!error) {
            this.meteo = cheerio.load(body);
            ipcRenderer.send('modifyIcon');
            this.setTimer();
            console.log('Данные обновлены!');
          } else {
            console.log('Произошла ошибка: ' + error);
          }
        });
      }
    }
  };
</script>

<template>
  <v-card class="pa-5 mt-4 fill-height">
    <v-card-text>
      <v-layout>
          <v-icon class="pr-5" large @click="loadData()" >mdi-refresh</v-icon>
        <v-select
          :items="timeouts"
          label="Update timeout"
          v-model="updateTime"
          @change="loadData()"
          class="pr-3"
        ></v-select>
        <v-select
          :items="cities"
          label="Select city"
          v-model="city"
          @change="loadData()"
        ></v-select>
      </v-layout>
      <div v-html="html">
      </div>
    </v-card-text>
  </v-card>
</template>


<style>
  .Block {
    display: grid;
  }

  .TitleRow {
    padding-top: 16px;
    font-size: 18px;
    border-bottom: 1px solid #a7a7a7;
  }

  .Row, .TitleRow2 {
    display: grid;
    padding: 16px;
    grid-template-columns: repeat(7, 1fr);
  }
</style>
