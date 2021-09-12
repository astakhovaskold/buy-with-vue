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
        <td>{{ item.quantity }}</td>
        <td>{{ exchange(item.price) }}</td>
        <td>
          <button @click="$emit('deleted', {thing: item, key})">Удалить</button>
        </td>
      </tr>
      </tbody>
      <tfoot>
        <tr>
          <td colspan="2"></td>
          <td>Итого:</td>
          <td>
            <b>{{ exchange(total) }} RUB</b>
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
    exchange: Function
  },
  data() {
    return {

    }
  },
  computed: {
    total() {
      return this.cart.reduce((prev, cur) => (prev + cur.price) * cur.quantity, 0)
    }
  }
}
</script>

<style scoped>
  .cart {
    margin: 120px auto 0;
  }
</style>