<script setup>
  import { computed, ref } from 'vue';
  import Card from './components/Card.vue';
  import Data from "./assets/data.json";

  const itemsCard = ref(Data.trains);
  const selectedTrainName = ref(null);
  const selectedRegion = ref(null);
  const selectedMonth = ref(null);

  const trainNameList = computed(() => {
    return itemsCard.value.map(train => train.name);
  });

  const trainRegionList = computed(() => {
    return [... new Set(itemsCard.value.map(train => train.region))];
  });

  const monthList = computed(() => {
    const monthNames = {
      'Январь': 0, 'Февраль': 1, 'Март': 2, 'Апрель': 3,
      'Май': 4, 'Июнь': 5, 'Июль': 6, 'Август': 7,
      'Сентябрь': 8, 'Октябрь': 9, 'Ноябрь': 10, 'Декабрь': 11
    };

    return Object.entries(monthNames).map(([name, index]) => ({
      title: name,
      value: index
    }));
  });

  const filteredTrains = computed(() => {
    let trains = itemsCard.value;
    
    // Фильтруем по названию поезда
    const searchName = selectedTrainName.value;
    if (searchName) {
      trains = trains.filter(train => train.name === searchName);
    };

    // Фильтруем по названию региона
    const regionName = selectedRegion.value;
    if (regionName) {
      trains = trains.filter(train => train.region === regionName);
    }

    // Фильтруем по номеру месяца все рейсы данного направления
    const monthIndexName = selectedMonth.value;
    if (monthIndexName) {
      trains = trains.filter(train => {
        return train.departures.some(departuresDateStr => {
          const departureDate = new Date(departuresDateStr);

          return departureDate.getMonth() === monthIndexName;
        })
      });
    };
     
    return trains;
  });

</script>

<template>
  <v-container>
    <v-row>
      <v-col cols="12" sm="6">
        <v-autocomplete
          v-model="selectedTrainName"
          clearable
          label="Название поезда"
          :items="trainNameList"
          variant="outlined"
        >
        </v-autocomplete>
      </v-col>
      <v-col cols="6" sm="3">
        <v-select
          v-model="selectedRegion"
          clearable
          chips
          label="Регион"
          :items="trainRegionList"
          variant="outlined"
        >
        </v-select>
      </v-col>
      <v-col cols="6" sm="3">
        <v-select
          v-model="selectedMonth"
          clearable
          chips
          label="Месяц"
          :items="monthList"
          variant="outlined"
        >
        </v-select>
      </v-col>
    </v-row>
    <v-row>
      <v-col 
        v-for="(data, index) in filteredTrains"
        :key="index"
        cols="12"
        sm="6" 
        md="4"
      >
        <Card :item="data"/>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped></style>
