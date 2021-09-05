<script>
import VuecCalendar from "./core/calendar.vue";
import dayjs from "dayjs";
export default {
  extends: VuecCalendar,
  props: {
    value: {
      type: Array,
      default: () => [],
    },
    format: {
      type: String,
      default: "YYYY-MM-DD",
    },
    date: {
      type: Object,
      default: () => dayjs(),
    },
  },
  data() {
    return {
      localSelection: [].concat(this.value),
    };
  },
  computed: {
    monthSelections() {
      const map = this.localSelection.map((date) => date.format("YYYY-MM-DD"));
      this.$emit("selected-dates", map);
      return map;
    },
  },
  watch: {
    value(newValue) {
      this.dateUnderCursor = null;
      this.localSelection = newValue;
    },
  },
  methods: {
    getDayData({ dayKey, monthKey, date }) {
      const data = (this.data[monthKey] || {})[dayKey] || {};
      const start = this.localSelection[0] || this.dateUnderCursor;
      const end = this.localSelection[1] || this.dateUnderCursor;

      return Object.assign({}, data, {
        class: [
          data.class,
          {
            hover: this.dateUnderCursor && date.isSame(this.dateUnderCursor),
            start: this.localSelection.length > 0 && date.isSame(start, "day"),
            end: this.localSelection.length > 1 && date.isSame(end, "day"),
            selected:
              this.localSelection.length > 1 &&
              date.isBetween(start, end, "days", "[]"),
            highlight:
              this.dateUnderCursor &&
              this.localSelection.length === 1 &&
              date.isBetween(start, this.dateUnderCursor, "days", "[]"),
          },
        ],
      });
    },

    onHover(date) {
      if (this.dateUnderCursor !== date) {
        this.dateUnderCursor = date;
      }
    },
    onDayClick({ date }) {
      if (
        this.localSelection.length === 0 ||
        this.localSelection[0] === undefined
      ) {
        this.$set(this.localSelection, 0, date);
      } else if (this.localSelection.length !== 1) {
        this.localSelection.length = 0;
        this.localSelection.push(date);
      }
      if (date.isAfter(this.localSelection[0], "day")) {
        this.localSelection.push(date);
      } else {
        this.$set(this.localSelection, 0, date);
      }
      this.$emit("input", this.localSelection);
    },
  },
};
</script>

<style lang="scss">
.vuec-theme-orange {
  .vuec-day {
    color: #131b1f;
    &.disabled {
      .vuec-day-content {
        border: none;
        cursor: not-allowed;
      }
    }
    // .vuec-day-content {
    //   display: flex;
    //   top: 2px;
    //   left: 2px;
    //   right: 2px;
    //   bottom: 2px;
    //   height: calc(50% - 1px);
    //   width: calc(50% - 1px);
    //   border-radius: 3px;
    //   border: 1px solid hsla(0,0%,59%,.7);
    // }

    .vuec-default-day {
      display: flex;
      flex: 1;
      align-items: center;
      justify-content: center;
      position: relative;
      border-radius: 7px;
      padding: 4px;
      // border: 2px solid transparent;
    }
    &:not(.disabled) {
      cursor: pointer;
      &.selected {
        background: none;
      }
      &.highlight {
        background: none;
      }
      &.selected .vuec-day-content,
      &.highlight .vuec-day-content {
        background-color: #efefef;
        border-radius: 7px;
        border-color: #ccc;
      }
      &.hover .vuec-day-content {
        // border: 2px solid #333;
        color: #131b1f;
        text-decoration: none;
        // border: 0;
        &:before,
        &:after {
          display: none;
        }
      }
      &.start .vuec-day-content,
      &.end .vuec-day-content {
        background-color: #c2d833;
        border-radius: 7px;
        color: white;
      }
      &:before,
      &:after {
        display: none;
      }
    }
  }
  .vuec-week-nav .col {
    font-size: 0.75em;
  }

  .vuec-month-name {
    h2 {
      margin: 10px 0;
    }
  }

  .vuec-nav {
    position: relative;
    .vuec-btn-prev,
    .vuec-btn-next {
      position: absolute;
      top: 0;
      cursor: pointer;
      padding: 5px;
    }
    .vuec-btn-prev {
      right: 0;
      padding-right: 0;
    }
    .vuec-btn-next {
      left: 0;
      padding-left: 0;
    }
  }
}

.disabled .vuec-default-day {
  opacity: 0.5;
}
.disabled .vuec-default-day::before {
  content: " ";
  top: 50%;
  left: 50%;
  height: 1px;
  width: 1rem;
  position: absolute;
  background-color: #333;
  transform: translate(-50%, -50%);
}
.vuec-default-day:hover {
  border: 2px solid #333;
}
.vuec-day-content {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
}

.vuec-month .vuec-7col .vuec-col {
  max-width: 14.285714285714286% !important;
  flex: 1 1 14.28571%;
}
</style>
