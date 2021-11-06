<template>
  <div class="vuec-range-input">
    <div class="vuec-date-inputs" @click="showPicker">
      <div class="input" :class="{ fromDate: activeTab == 'fromDate' }">
        <div class="icon__custom">
          <IconCalendar />

          <template v-if="value[0]">
            {{ value[0] }}
          </template>
          <template v-else>
            {{ labels[0] }}
          </template>
        </div>
      </div>
      <div class="input" :class="{ toDate: activeTab == 'toDate' }">
        <div class="icon__custom">
          <IconCalendar />

          <template v-if="value[1]">
            {{ value[1] }}
          </template>
          <template v-else>
            {{ labels[1] }}
          </template>
        </div>
      </div>
    </div>
    <Transition name="popup-animation">
      <div
        v-if="visible"
        :class="{ mobile: mobile }"
        class="vuec-popup"
        @click="onClickDelegate"
      >
        <div v-if="mobile" class="vuec-popup-header">
          <slot name="title"> تاریخ ورود و خروج </slot>
          <div class="vuec-popup-close" @click="showPicker(false)">
            <IconClose />
          </div>
        </div>
        <VuecSelectRange
          :theme="theme"
          :value="dates"
          :date="date"
          :disabled_dates="disabled_dates"
          :min-date="minDate"
          :max-date="maxDate"
          :visible-months="2"
          :prices="prices"
          :selectable="true"
          @input="onSelectionChange"
        />
      </div>
    </Transition>
  </div>
</template>

<script>
import VuecSelectRange from "./select-range.vue";
import IconClose from "./icons/close.vue";
import IconCalendar from "./icons/calendar.vue";
import dayjs from "../date";

export default {
  components: {
    VuecSelectRange,
    IconClose,
    IconCalendar,
  },
  props: {
    disabled_dates: {
      type: Array,
      default: () => [],
    },
    theme: {
      type: String,
      default: "default",
    },
    prices: {
      type: Array,
      required: false,
      default: null,
    },
    mobile: {
      type: Boolean,
      default: false,
    },
    selectable: {
      type: Boolean,
      default: false,
    },
    selectionMode: {
      type: String,
      default: "single",
    },
    date: {
      type: Object,
      default: () => dayjs(),
    },
    data: {
      type: Object,
      default: () => ({}),
    },
    minDate: {
      type: [Object, String],
      default: null,
    },
    maxDate: {
      type: [Object, String],
      default: null,
    },
    visibleMonths: {
      type: Number,
      default: 1,
    },
    value: {
      type: Array,
      default: () => [],
    },
    labels: {
      type: Array,
      default: () => ["تاریخ ورود", "تاریخ خروج"],
    },
    open: {
      type: Boolean,
      default: false,
    },
    format: {
      type: String,
      default: "YYYY-MM-DD",
    },
  },
  data() {
    const [fromDate = this.date, toDate = this.date] = this.value;
    return {
      visible: this.open,
      temporaryDisableClickListen: false,
      fromDate,
      toDate,
      dates: [],
      activeTab: "",
    };
  },
  computed: {
    formattedDates() {
      return this.dates.map((date) => date.format(this.format));
    },
  },
  watch: {
    open(newValue) {
      this.showPicker(newValue);
    },
  },
  mounted() {
    this.showPicker(this.open);
    document.body.addEventListener("click", this.handleBodyClick);
  },
  beforeDestroy() {
    document.body.removeEventListener("click", this.handleBodyClick);
  },
  methods: {
    handleBodyClick() {
      if (this.visible && !this.temporaryDisableClickListen) {
        this.visible = false;
        this.$emit("hide");
      }
      this.temporaryDisableClickListen = false;
    },
    showPicker(shown = true) {
      this.temporaryDisableClickListen = shown;
      this.visible = shown;
    },
    onClickDelegate($event) {
      $event.stopPropagation();
    },
    onSelectionChange(date) {
      let fromDate = dayjs(date[0]);
      let toDate = dayjs(date[date.length - 1]);
      if (date.length > 1) {
        this.dates = [fromDate, toDate];
        this.visible = false;
        this.$emit(
          "selected-dates",
          this.dates.map((date) => date.format(this.format))
        );
      }
    },
  },
};
</script>

<style lang="scss">
.vuec-range-input {
  position: relative;
  .vuec-popup {
    flex-direction: column;
    width: 600px;
    background: #fff;
    box-shadow: 0 15px 12px rgba(0, 0, 0, 0.22), 0 0 38px rgba(0, 0, 0, 0.3);
    border-radius: 4px;
    padding: 16px;
    position: relative;
    z-index: 99;
    position: absolute;
    top: 100%;
  }
  .vuec-popup-header {
    padding: 10px 20px;
    height: 30px;
    border-bottom: 1px solid orange;
    .vuec-popup-close {
      float: left;
      border-radius: 99em;
      border: 1px solid;
      width: 30px;
      height: 30px;
      display: flex;
      justify-content: center;
      align-items: center;
      color: orange;
      fill: orange;
      cursor: pointer;
    }
  }
  .vuec-date-inputs {
    align-items: center;
    box-sizing: border-box;
    border: 1px solid #e0e0e0;
    border-radius: 2px;
    display: flex;
    flex: 0 0 400px;
    height: 48px;
    width: 300px;
    .input {
      flex: 1;
      padding: 16px;
    }
  }
  .mobile {
    position: fixed;
    top: 0;
    bottom: 0;
    width: 90%;
    padding: 0;
    overflow: scroll;
    &.popup-animation-enter-active,
    &.popup-animation-leave-active {
      transition: right 0.5s;
      right: 0;
    }
    &.popup-animation-enter,
    &.popup-animation-leave-to {
      right: -100%;
    }
    .vuec-calendar {
      width: 100%;
    }
    .vuec-nav {
      display: none;
    }
    .vuec-month-wrapper {
      flex-direction: column;
    }
  }
}
.fromDate {
  background: blue;
}
.toDate {
  background: black;
}
.icon__custom {
  display: flex;
  align-content: center;
  align-items: center;
}
.icon__custom svg {
  margin-left: 5px;
}
</style>