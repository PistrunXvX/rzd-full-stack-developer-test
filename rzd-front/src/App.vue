<script setup>
  import { onMounted, ref } from 'vue';
  import Card from './components/Card.vue';
  import Data from "./assets/data.json";

  const itemsCard = ref(Data.trains);

  // onMounted(() => {
  //     console.log(itemsCard.value);
  // });
</script>

<template>
  <v-container>
    <v-row>
      <v-col 
        v-for="(data, index) in itemsCard"
        :key="index"
        cols="12"
        sm="6" 
        md="4"
      >
        <Card :item="data"/>
      </v-col>
      <!-- <v-col cols="12" sm="6" md="4">
        <Card />
      </v-col>
            <v-col cols="12" sm="6" md="4">
        <Card />
      </v-col>
            <v-col cols="12" sm="6" md="4">
        <Card />
      </v-col>
            <v-col cols="12" sm="6" md="4">
        <Card />
      </v-col>
      <v-col cols="12" sm="6" md="4">
        <Card />
      </v-col> -->
    </v-row>
  </v-container>
</template>

<style scoped></style>
