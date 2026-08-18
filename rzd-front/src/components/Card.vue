
<script setup>
    import { computed, onMounted, ref, useId } from 'vue';

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
    const detailsDialog = ref(false);

    const nearestDate = computed(() => getNearestDeparture(props.item.departures));
    const uniqueDialogId = useId();

    const allDateList = props.item.departures.map(dateStr => {
        return new Date(dateStr).toLocaleDateString('ru-RU', {
            day: "numeric",
            month: "short",
            year: "numeric"
        });
    });

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

    onMounted(() => {
        data.value.departure = props.item.route[0];
        data.value.arrival = props.item.route.at(-1);
    });
</script>

<template>
  <v-card
    class="mx-auto"
    outlined
    rounded="md"
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
        @click="detailsDialog = true"
      >
        Подробнее
      </v-btn>
      <v-spacer></v-spacer>
      <div class="subtitle-1">
        от {{ props.item.price_from }} ₽
      </div>
    </v-card-actions>

    <v-dialog :id="uniqueDialogId" v-model="detailsDialog" scrollable max-width="600px">
      <v-card rounded="md">
        <v-card-title class="d-flex align-center px-6 pt-6 pb-2">
          <div class="text-h5 font-weight-bold">{{ props.item.name }}</div>
          <v-spacer></v-spacer>
          <v-btn icon="mdi-close" variant="text" @click="detailsDialog = false"></v-btn>
        </v-card-title>

        <v-divider></v-divider>

        <v-card-text class="px-6 py-4" style="height: 400px;">
          <!-- Описание -->
          <p class="text-body-1 mb-6 text-grey-darken-2">
            {{ props.item.description }}
          </p>

          <!-- Теги -->
          <div class="mb-6">
            <v-chip
              v-for="tag in props.item.tags"
              :key="tag"
              class="ma-1"
              color="primary"
              variant="tonal"
              size="small"
              label
            >
              <v-icon start size="14">mdi-tag-outline</v-icon>
              {{ tag }}
            </v-chip>
          </div>

          <!-- Маршрут -->
          <v-list-item class="pa-0 mb-6">
             <template v-slot:prepend>
                <v-icon color="grey">mdi-map-marker-path</v-icon>
             </template>
             <v-list-item-title class="font-weight-bold">Полный маршрут</v-list-item-title>
             <v-list-item-subtitle class="text-wrap mt-1">
                {{ props.item.route.join(' &rarr; ') }}
             </v-list-item-subtitle>
          </v-list-item>

          <v-row>
            <!-- Все даты отправления -->
            <v-col cols="12" sm="6" class="pb-0">
              <v-list lines="one" class="pa-0">
                <v-list-subheader class="font-weight-bold pl-0 color-black">
                  <v-icon start>mdi-calendar-range</v-icon> Даты отправления
                </v-list-subheader>
                <v-list-item
                  v-for="date in allDateList"
                  :key="date"
                  class="pl-0"
                >
                  <v-list-item-title class="text-body-2">
                      {{ date }}
                  </v-list-item-title>
                </v-list-item>
              </v-list>
            </v-col>

            <!-- Список экскурсий -->
            <v-col cols="12" sm="6">
              <v-list lines="one" class="pa-0">
                <v-list-subheader class="font-weight-bold pl-0 color-black">
                   <v-icon start>mdi-image-ferris-wheel</v-icon> Включенные экскурсии
                </v-list-subheader>
                <v-list-item
                  v-for="excursion in props.item.excursions"
                  :key="excursion"
                  class="pl-0"
                >
                  <v-list-item-title class="text-body-2 text-wrap">
                    {{ excursion }}
                  </v-list-item-title>
                </v-list-item>
              </v-list>
            </v-col>
          </v-row>

        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions class="px-6 py-4">
          <div class="text-h6 font-weight-bold color-primary">
            от {{ props.item.price_from.toLocaleString('ru-RU') }} ₽
          </div>
          <v-spacer></v-spacer>
          <v-btn
            color="primary"
            variant="flat"
            rounded="md"
            append-icon="mdi-open-in-new"
            :href="props.item.buy_url"
            target="_blank"
            rel="noopener noreferrer"
          >
            Купить билет
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>
</template>
