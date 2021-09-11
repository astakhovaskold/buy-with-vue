<template>
  <v-row justify="center">
    <v-expansion-panels accordion>
      <v-expansion-panel>
        <v-expansion-panel-header>{{ group }}</v-expansion-panel-header>
        <v-expansion-panel-content v-for="(thing, key) in things" :key="key">
          {{ thing.name }}
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
    data: () => ({

    }),
    created() {
      // console.log(this.thing)
    }
  }
</script>
