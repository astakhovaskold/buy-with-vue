<template>
  <v-simple-table class="cart">
    <template v-slot:default>
      <thead>
      <tr>
        <th class="text-left">
          Наименование товара и описание
        </th>
        <th class="text-left">
          Количество
        </th>
        <th class="text-left">
          Цена
        </th>
        <th></th>
      </tr>
      </thead>
      <tbody>
      <tr
          v-for="(item, key) in cart"
          :key="key"
      >
        <td>{{ item.name }}</td>
        <td class="quantity">
          <div><input type="number" :value="item.quantity" @change="quantity($event, item)" class="quantity-input"> шт.</div>
          <div v-if="(item.store - item.quantity) <= low" class="low">Количество ограничено</div>
        </td>
        <td><span class="big">{{ format(exchange(item.price, 'usd')) }}</span>/шт.</td>
        <td>
          <button @click="$emit('deleted', item)">Удалить</button>
        </td>
      </tr>
      </tbody>
      <tfoot>
        <tr class="total">
          <td colspan="2"></td>
          <td class="total-text">Итого:</td>
          <td>
            <b>{{ format(exchange(total, 'usd')) }}</b>
          </td>
        </tr>
      </tfoot>
    </template>
  </v-simple-table>
</template>

<script>
export default {
  name: 'Cart',
  props: {
    cart: Array,
    exchange: Function,
    quantity: Function
  },
  data() {
    return {
      low: 2
    }
  },
  methods: {
    format(number) {
      return new Intl.NumberFormat('ru-RU', { style: 'currency', currency: 'RUB' }).format(number)
    }
  },
  computed: {
    total() {
      return this.cart.reduce((prev, cur) => (prev + cur.price) * cur.quantity, 0)
    }
  }
}
</script>

<style scoped lang="scss">
  .cart {
    margin: 120px auto 60px;

    tbody td {
      height: 90px !important;
    }
  }

  .big  {
    font-size: 18px;
    font-weight: bold;
  }

  .low {
    border: 1px dashed darkorange;
    background-color: #eee;
    padding: 5px;
    margin-top: 10px;
  }

  .quantity {
    display: flex;
    flex-direction: column;
    justify-content: center;

    &-input {
      width: 50px;
      padding: 5px;
      border: 1px solid darkgray;
      outline: none;
    }
  }

  .total {
    &-text {
      text-align: right;
    }
  }
</style>