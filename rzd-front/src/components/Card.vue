
<script setup>
import { computed, onMounted, ref } from 'vue';

    const props = defineProps({
        item: {
            type: Object,
            required: true,
        }
    });

    const data = ref({
        departure: "",
        arrival: "",
    });

    const nearestDate = computed(() => getNearestDeparture(props.item.departures));

    onMounted(() => {
        data.value.departure = props.item.route[0];
        data.value.arrival = props.item.route.at(-1);
    })

    function getNearestDeparture(depList) {
        const dateNow = new Date();

        dateNow.setHours(0, 0, 0, 0);

        const upcomingDates = depList.map(d => new Date(d)).filter(d => d >= dateNow).sort((a, b) => a - b);
        const nearestDate = upcomingDates[0];

        return nearestDate.toLocaleDateString('ru-RU', {
            day: "numeric",
            month: "short",
            year: "numeric"
        });
    };
</script>

<template>
  <v-card
    class="mx-auto"
    outlined
  >
    <v-list-item three-line>
      <v-list-item-content>
        <v-list-item-title class="headline mb-1">
          {{ props.item.name }}
        </v-list-item-title>
        <v-list-item-subtitle>{{ props.item.region }}</v-list-item-subtitle>
      </v-list-item-content>

      <v-list-item-avatar
        tile
        size="80"
        color="grey"
      ></v-list-item-avatar>
    </v-list-item>

    <v-card-text>
      <v-row align="center">
        <v-col cols="12">
          <v-icon left>mdi-map-marker</v-icon>
          {{ data.departure }} &rarr; {{ data.arrival }}
        </v-col>
        <v-col cols="12">
          <v-icon left>mdi-clock</v-icon>
          {{ props.item.duration_days }} дней
        </v-col>
        <v-col cols="12">
          <v-icon left>mdi-calendar</v-icon>
            {{ nearestDate }}
        </v-col>
      </v-row>
    </v-card-text>

    <v-card-actions>
      <v-btn
        text
        color="primary"
        variant="flat"
      >
        Подробнее
      </v-btn>
      <v-spacer></v-spacer>
      <div class="subtitle-1">
        от {{ props.item.price_from }} ₽
      </div>
    </v-card-actions>
  </v-card>
</template>
