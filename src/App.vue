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
            <Accordion
                class="accordion"
                :things="item"
                :group="key"
                @added="addToCart"
            />
          </v-col>
        </v-row>

        <v-row v-if="cart.length">
          <Cart :cart="cart" @deleted="deleteFromCart" />
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Accordion from './components/Accordion';
import Cart from './components/Cart';
import axios from 'axios'

export default {
  name: 'App',
  components: {
    Accordion,
    Cart,
  },
  data() {
    return {
      goods: [],
      names: {},
      cart: [],
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
    addToCart(data) {
      const current = this.goods[data.thing.group][data.key]
      const cart = this.cart.find(c => c.id === data.thing.id)

      if (current.count) {
        if (cart) {
          cart.quantity++
          cart.count = data.thing.count
        } else {
          this.cart.push({quantity: 1, ...data.thing})
        }
      }

      current.count > 0 ? current.count-- : current.count = 0

      // console.log(this.cart)
    },
    deleteFromCart(data) {
      // console.log(data)

      const index = this.cart.findIndex(c => c.id === data.thing.id)
      const cart = this.cart[index]

      const currentIdx = this.goods[data.thing.group].findIndex(c => c.id === data.thing.id)
      const current = this.goods[data.thing.group][currentIdx]
      current.count++

      console.log(currentIdx)

      if (cart.quantity - 1) {
        cart.count = data.thing.count
        cart.quantity--
      } else {
        this.cart.splice(index, 1)
      }
    }
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

      this.goods = goods.reduce((r, item) => {
        const name = this.names[item.G];

        r[name.G] = r[name.G] || []

        const obj = {
          id: item.T,
          name: this.names[item.G]['B'][item.T]['N'],
          group: name.G,
          count: item.P,
          price: item.C,
        }

        r[name.G].push(obj)
        return r;
      }, {});

      // console.log(this.goods)
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

  .column {
    margin-bottom: 20px;
  }
</style>