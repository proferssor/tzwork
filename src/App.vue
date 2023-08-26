<script setup>
import { onMounted, ref } from "vue";
import { subDays, format } from "date-fns";
import ruLocale from "date-fns/locale/ru";
import Block from "./components/Block.vue";

const rangeDates = ref([]);
const urlsData = ref({});
const isLoading = ref(false);
const months = ref([]);

const getLastDays = () => {
  const today = new Date();
  const endDate = today;
  const startDate = subDays(today, 357);

  const dateRange = [];
  const monthSet = new Set();
  let currentDate = startDate;

  while (currentDate <= endDate) {
    dateRange.push(format(currentDate, "yyyy-MM-dd"));
    const monthName = format(currentDate, "LLL", { locale: ruLocale });
    monthSet.add(monthName);
    currentDate = subDays(currentDate, -1);
  }

  rangeDates.value = dateRange;
  months.value = [...monthSet];
  return dateRange;
};

const getByUrlDates = async () => {
  try {
    isLoading.value = true;
    const data = await fetch("https://dpg.gg/test/calendar.json");
    const json = await data.json();
    isLoading.value = false;
    urlsData.value = json;
  } catch (error) {
    console.log(error);
  }
};

onMounted(() => {
  getLastDays();
  getByUrlDates();
});
</script>

<template>
  <div>
    <h1>Last 375 Days</h1>
    <div v-if="isLoading">Loading...</div>
    <div v-else>
      <div class="days">
        <div class="weekDays">
          <div>Пн</div>
          <div>Вт</div>
          <div>Ср</div>
          <div>Чт</div>
          <div>Пт</div>
          <div>Сб</div>
          <div>Вс</div>
        </div>
        <div>
          <div class="months">
            <span v-for="item in months" :key="item">{{ item }}</span>
          </div>
          <ul>
            <li v-for="(day, index) in getLastDays()" :key="index">
              <Block :date="day" :commits="urlsData[day]" />
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
ul {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  gap: 2px;
  max-width: 819px;
  padding: 0;
}

.months {
  display: flex;
  max-width: 819px;
  justify-content: space-between;
}

.days {
  display: flex;
}

.weekDays {
  margin-top: 30px;
  margin-right: 4px;
}
</style>
