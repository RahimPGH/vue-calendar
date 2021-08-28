<template>
  <div>
    <!-- <div>
      <h2>Gregory</h2>
      <div>
        <h3>Range Input</h3>
        <VuecRangeInput theme="orange" />
      </div>
      <div>
        <h3>Single Input</h3>
        <VuecSingleInput theme="orange" />
      </div>
    </div> -->
    <div class="jalali">
      <h2>Jalali</h2>
      <div>
        <VuecRangeInput
          :date="todayInJalali"
          :selectable="true"
          :min-date="now"
          :max-date="maxDate"
          :mobile="false"
          :labels="['ورود', 'خروج']"
          theme="orange"
        />
      </div>
    </div>

    <!-- <div class="jalali">
      <h2>Jalali</h2>
      <div>
        <h3>select Range</h3>
        <VuecSelectRange
          :date="todayInJalali"
          :selectable="true"
          :min-date="now"
          :max-date="maxDate"
          :mobile="false"
          theme="orange"
        />
      </div>
    </div> -->
  </div>
</template>

<script>
import { VuecRangeInput, VuecSingleInput, VuecSelectRange } from "../../src";
import dayjs from "../../src/date";

export default {
  components: {
    VuecRangeInput,
    VuecSingleInput,
    VuecSelectRange,
  },
  data() {
    return {
      now: dayjs(),
      todayInJalali: dayjs().calendar("jalali").locale("fa"),
      maxDate: dayjs().add(46, "day"),
    };
  },
  mounted() {
    window.dayjs = dayjs;
  },
};
</script>

<style lang="scss">
.jalali {
  direction: rtl;
}
</style>
