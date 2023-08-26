<script setup>
import { parseISO, format } from "date-fns";
import { computed, ref } from "vue";
import ruLocale from "date-fns/locale/ru";

const props = defineProps({
  date: String,
  commits: Number,
});

const showTooltip = ref(false);

const color = computed(() => {
  if (props.commits <= 0) return "#ededed";
  else if (props.commits <= 9) return "#ACD5F2";
  else if (props.commits <= 19) return "#7FA8C9";
  else if (props.commits <= 29) return "#527BA0";
  else if (props.commits > 29) return "254E77";
  else return "#ededed";
});

const parsedDate = parseISO(props.date);
const formattedDate = format(parsedDate, "EEEE, MMMM dd, yyyy", {
  locale: ruLocale,
});
</script>

<template>
  <div class="block" :style="{ background: color }">
    <div class="tooltip">
      <p v-if="props.commits">{{ props.commits }} contributions</p>
      <p v-else>No contributions</p>
      <span>
        {{ formattedDate }}
      </span>
    </div>
  </div>
</template>

<style scoped>
.block {
  background: #ededed;
  width: 14px;
  height: 14px;
  position: relative;
}

.block:hover {
  border: 1px solid black;
}

.block:hover .tooltip {
  display: block;
}
.tooltip {
  width: max-content;
  max-width: 500px;
  text-align: center;
  position: absolute;
  background-color: #000000;
  border-radius: 3px;
  color: white;
  padding: 5px 9px;
  border-radius: 3px;
  z-index: 999;
  transform: translateX(-50%);
  top: -54px;
  display: none;
}

p {
  margin: 0;
}

span {
  color: #7c7c7c;
  font-family: Inter;
  font-weight: 400;
}
</style>