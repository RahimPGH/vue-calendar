<template>
  <div
    :class="[
      data.class,
      { disabled, selected, selectable },
      prices ? (checkDate(date.format(`YYYY-MM-DD`)) ? '' : 'disabled') : '',
    ]"
    class="vuec-day"
    @mouseover="$emit('hover', date)"
    @mouseout="$emit('blur', date)"
    @click="onClick(date)"
  >
    <div class="vuec-day-content">
      <slot :data="data" :date="date" :prices="prices" name="day">
        <div class="vuec-default-day">
          {{ date.format("D") }}
        </div>
      </slot>
    </div>
    <div class="vuec-square-placeholder" />
  </div>
</template>

<script>
export default {
  props: {
    index: {
      type: String,
      required: true,
    },
    date: {
      type: Object,
      default: null,
    },
    data: {
      type: Object,
      default: () => ({}),
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    selected: {
      type: Boolean,
      default: false,
    },
    selectable: {
      type: Boolean,
      default: false,
    },
    prices: {
      type: Array,
      default: null,
    },
  },
  methods: {
    onClick(date) {
      let disableDay = true;
      if (this.prices != null) {
        disableDay = this.checkDate(date.format(`YYYY-MM-DD`));
      }
      if (this.selectable && disableDay) {
        this.$emit("click", {
          key: this.index,
          date: this.date,
          data: this.data,
          selected: this.selected,
          selectable: this.selectable,
        });
      }
    },
    checkDate(param) {
      if (this.prices != null) {
        let finded = "";
        this.prices.find((item) => {
          // console.log(item.date === param);
          finded = item.date === param;
          return finded;
        });
        return finded;
      }
    },
  },
};
</script>

<style lang="scss">
.vuec-day {
  position: relative;
  padding: 0;

  .vuec-square-placeholder {
    margin-bottom: 100%;
  }

  &[aria-hidden="true"] {
    opacity: 0;
    display: none;
    pointer-events: none;
    @media (min-width: 768px) {
      display: block;
    }
  }

  .vuec-day-content {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
}
</style>
