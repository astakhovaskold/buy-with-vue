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
                :exchange="USDConvert"
                @added="addToCart"
            />
          </v-col>
        </v-row>

        <v-row v-if="cart.length">
          <Cart
              :cart="cart"
              :exchange="USDConvert"
              :quantity="changeQuantity"
              @deleted="deleteFromCart"
          />
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
      currency: {
        usd: 76.8
      }
    }
  },
  methods: {
    USDConvert(usd) {
      return (+usd * this.currency.usd).toFixed(2)
    },
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
    },
    deleteFromCart(thing) {
      this.changeQuantity(0, thing)
    },
    removeFromCart(thing) {
      const index = this.getCartItemIndex(thing)
      this.cart.splice(index, 1)
    },
    changeQuantity(e, thing) {
      const cartItem = this.getCartItem(thing)
      const goodsItem = this.getGoodsItem(thing)

      const val = e.target?.value || e
      const quantity = val > cartItem.store ? cartItem.store : val

      const diff = quantity < 0 ? cartItem.quantity : cartItem.quantity - quantity
      cartItem.quantity = cartItem.quantity + -diff
      goodsItem.count = goodsItem.count - -diff

      if (val > cartItem.store) {
        e.target.value = cartItem.store
      }

      if (cartItem.quantity <= 0) {
        this.removeFromCart(thing)
      }
    },
    getCartItemIndex(thing) {
      return this.cart.findIndex(c => c.id === thing.id)
    },
    getCartItem(thing) {
      return this.cart.find(c => c.id === thing.id)
    },
    getGoodsItem(thing) {
      const currentIdx = this.goods[thing.group].findIndex(c => c.id === thing.id)
      return this.goods[thing.group][currentIdx]
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
          store: item.P,
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