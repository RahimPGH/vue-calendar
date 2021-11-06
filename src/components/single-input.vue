<template>
  <div class="vuec-single-input">
    <div class="vuec-date-inputs" @click="showPicker">
      <div class="input">
        {{ formattedDate }}
      </div>
      <span  v-if="visible === false && formattedDate == '' && value == ''">{{
        placeholder
      }}</span>
      <span class="value_datepicker" v-if="formattedDate == '' ">{{ value }}</span>
    </div>
    <div v-show="visible" class="vuec-popup" @click="onClickDelegate">
      <VuecSelectSingle
        :theme="theme"
        :date="date"
        :value="selection"
        :visible-months="visibleMonths"
        :selectable="true"
        :min-date="minDate"
        :max-date="maxDate"
        @input="onSelectionChange"
        :disabled_dates="disabled_dates"
      />
    </div>
  </div>
</template>

<script>
import VuecSelectSingle from "./select-single.vue";
import dayjs from "../date";
export default {
  components: {
    VuecSelectSingle,
  },
  props: {
    format: {
      type: String,
      default: "YYYY-MM-DD",
    },
    theme: {
      type: String,
      default: "default",
    },
    placeholder: {
      type: String,
      default: "",
    },
    selectable: {
      type: Boolean,
      default: false,
    },
    selectionMode: {
      type: String,
      default: "single",
    },
    data: {
      type: Object,
      default: () => ({}),
    },
    date: {
      type: [Object, String],
      default: undefined,
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
      type: String,
      default: "",
    },
    open: {
      type: Boolean,
      default: false,
    },
    disabled_dates: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      visible: this.open,
      temporaryDisableClickListen: false,
      selection: undefined,
    };
  },
  computed: {
    formattedDate() {
      return this.selection ? this.selection.format("YYYY-MM-DD") : "";
    },
  },
  watch: {
    open(newValue) {
      this.visible = newValue;
      this.temporaryDisableClickListen = true;
    },
  },
  mounted() {
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
    showPicker() {
      this.temporaryDisableClickListen = true;
      this.visible = true;
    },
    onClickDelegate($event) {
      $event.stopPropagation();
    },
    onSelectionChange({ date }) {
      this.selection = dayjs(date);
      this.$emit("input", date.format(this.format));
      this.visible = false;
    },
  },
};
</script>

<style lang="scss">
.vuec-single-input {
  .vuec-popup {
    flex-direction: column;
    width: 400px;
    background: #fff;
    box-shadow: 0 15px 12px rgba(0, 0, 0, 0.22), 0 0 38px rgba(0, 0, 0, 0.3);
    border-radius: 4px;
    padding: 16px;
    direction: rtl;
    position: absolute;
    z-index: 99;
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
    }
  }
  .vuec-btn-next,
  .vuec-btn-prev {
    width: 40px;
    height: 40px;
    background: #fff;
    border-radius: 99em;
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    top: calc(50% - 20px);
    box-shadow: 0 0 4px rgba(0, 0, 0, 0.12), 0 4px 4px rgba(0, 0, 0, 0.24);
  }
  .vuec-btn-next {
    left: -20px;
  }
  .vuec-btn-prev {
    right: -20px;
  }
}
</style>