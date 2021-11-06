<template>
  <div>
    <div class="jalali">
      <h2>Jalali</h2>
      {{ selected_range }}
      <div>
        <VuecSingleInput
          :date="todayInJalali"
          :selectable="true"
          value="1400-07-27"
          :min-date="now"
          :disabled_dates="disable_dates"
          @input="singleInputback"
          theme="orange"
          placeholder="مبدا"
        />
      </div>
    </div>

    <div class="jalali">
      <h2>Jalali</h2>
      {{ selected_range }}
      <div>
        <VuecRangeInput
          :date="todayInJalali"
          :selectable="true"
          :disabled_dates="disable_dates"
          :value="selected_range"
          :min-date="now"
          @selected-dates="back"
          :labels="['ورود', 'خروج']"
          theme="orange"
        />
      </div>
    </div>

    <div class="jalali">
      <h2>Jalali</h2>
      {{ selected_range }}
      <div>
        <VuecSelectRange
          :date="todayInJalali"
          :min-date="now"
          :max-date="maxDate"
          :visible-months="2"
          :selectable="true"
          :selections="[1, 2, 3, 4, 6]"
          :show-previous-weeks="true"
          @selected-dates="select"
          :prices="prices"
          theme="orange"
        />
      </div>
    </div>
  </div>
</template>

<script>
const _ = require("lodash");

import {
  VuecRangeInput,
  VuecSingleInput,
  VuecSelectRange,
  VuecSelectSingle,
} from "../../src";
import dayjs from "../../src/date";

export default {
  components: {
    VuecRangeInput,
    VuecSelectSingle,
    VuecSingleInput,
    VuecSelectRange,
  },
  data() {
    return {
      now: dayjs(),
      todayInJalali: dayjs().calendar("jalali").locale("fa"),
      maxDate: dayjs().add(90, "day"),
      dates: [],
      selected_range: [],
      disable_dates: [
        { date: "1400-07-04" },
        { date: "1400-07-05" },
        { date: "1400-07-06" },
        { date: "1400-07-07" },
        { date: "1400-07-08" },
      ],
      prices: [
        {
          date: "1400-07-10",
          price: 560,
        },
        {
          date: "1400-07-15",
          price: 580,
        },
      ],
    };
  },
  methods: {
    singleInputback(date) {
      console.log(date);
    },
    back(dates) {
      this.selected_range = dates;
    },
    select(date) {
      this.selected_range = date;
    },
    clickDay(date) {
      console.log("slkj");
      console.log(date);
    },
  },
  mounted() {
    window.dayjs = dayjs;
  },
  created() {
    // this.prices = _.keyBy(this.prices, "date");
  },
};
</script>

<style lang="scss">
.jalali {
  direction: rtl;
}
</style>
