<template>
  <v-row justify="center">
    <v-expansion-panels accordion>
      <v-expansion-panel>
        <v-expansion-panel-header>{{ group }}</v-expansion-panel-header>
        <v-expansion-panel-content>
          <v-simple-table>
            <template>
              <tbody>
              <tr
                  v-for="(thing, key) in things"
                  :key="key"
                  @click="$emit('added', {thing, key})"
              >
                <td>{{ thing.name }} ({{ thing.count }})</td>
                <td class="table-price">{{ thing.price }}</td>
              </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-expansion-panels>
  </v-row>
</template>

<script>
  export default {
    name: 'Accordion',
    props: {
      things: Array,
      group: String
    },
    data() {
      return {

      }
    },
    created() {
      // console.log(this.thing)
    }
  }
</script>

<style scoped>
  .table-price {
    background-color: #eee;
    text-align: center;
  }
</style>
