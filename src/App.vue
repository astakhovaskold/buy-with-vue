<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row>
          <v-col
              v-for="(item, key, idx) in goods"
              :key="idx"
              cols="12"
              sm="4"
              class="column"
          >
            <Accordion class="accordion" :things="item" :group="key"/>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Accordion from './components/Accordion';
import axios from 'axios'

export default {
  name: 'App',

  components: {
    Accordion,
  },
  data() {
    return {
      goods: [],
      names: {},
      result: [],
    }
  },
  methods: {
    async getDataByFetch(url) {
      const response = await fetch(url, {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json'
        }
      })

      if (!response.ok) {
        throw new Error(`Ошибка по адресу ${response.url}, статус ошибки ${response.status}`);
      }

      return await response.json();
    },
  },
  created() {
    (async () => {
      const goods = await axios
          .get('http://localhost:8080/data/data.json')
          .then(res => res.data.Value.Goods)

      const names = await axios
          .get('http://localhost:8080/data/names.json')
          .then(res => res.data)

      Object.assign(this.names, names)

      // const namesArray = Object.entries(names)

      // this.goods = goods.map(item => item.G = t)

      this.goods = goods.reduce((r, item) => {
        const name = this.names[item.G];
        r[name.G] = r[name.G] || []
        item.name = this.names[item.G]['B'][item.T]['N']
        r[name.G].push(item)
        return r;
      }, {});

      /*const res = [];
      for (const key in map) {
        res.push(map[key]);
      }*/

      // this.goods = this.goods.map(item => item.G = )

      // this.goods = res
      // console.log(map)
    })();
  }
};
</script>

<style scoped lang="scss">
  .container {
    margin-top: 40px;
  }

  .accordion {
    padding: 0 15px;
  }
</style>