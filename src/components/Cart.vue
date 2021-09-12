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
        <td>{{ item.price }}</td>
        <td>
          <button @click="$emit('deleted', {thing: item, key})">Удалить</button>
        </td>
      </tr>
      </tbody>
    </template>
  </v-simple-table>
</template>

<script>
export default {
  name: 'Cart',
  props: {
    cart: Array
  },
  data() {
    return {

    }
  },
  updated() {
    console.log('Cart', this.cart)
  }
}
</script>

<style scoped>
  .cart {
    margin: 120px auto 0;
  }
</style>