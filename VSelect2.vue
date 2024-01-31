<!--
Vue-select component for multi/single select menu
Props:
    multiple: { Boolean},
    closeOnSelect: { Boolean},
    clearable: { Boolean},
    disabled: { Boolean},
    placeholder: { String},
    name: { String},
    options:  [Array, Object],
    label: { String },
    reduce: { Function },
    noOptionsText: { String},
    showLoadingIcon: { Boolean,
    hideSelectAll: { Boolean},
    getOptionLabel: { Function},
---------------------------------------
Events:
@input // on input
@change // on change
@v-select-focus // on focus
@v-select-blur // on blur
-->
<template>
  <vue-select
      v-model="selected"
      :dir="isRTL ? 'rtl' : 'ltr'"
      :multiple="multiple"
      :close-on-select="closeOnSelect_v"
      :clearable="clearable"
      :placeholder="placeholder"
      :name="name"
      :options="options"
      :label="label"
      :reduce="reduce"
      :disabled="disabled"
      :value="selected"
      :loading="showLoadingIcon"
      :get-option-label="getOptionLabel"
      @input="onChange"
      @search:focus="onFocus"
      @search:blur="onBlur"
  >
    <template
        v-if="showSelectAll"
        #list-header
    >
      <div class="d-flex align-items-center p-1">
        <!--        <b-form-checkbox-->
        <!--          id="vue-select-multi-all"-->
        <!--          v-model="checkAll"-->
        <!--          name="selectAll"-->
        <!--          class="d-block w-auto"-->
        <!--          :checked="selected.length === options.length"-->
        <!--          :value="true"-->
        <!--          :unchecked-value="false"-->
        <!--          @change="checkAllOptions"-->
        <!--        >{{ $t('translate.selectAll') }}-->
        <!--        </b-form-checkbox>-->

        <input
            v-model="checkAll"
            type="checkbox"
            name="selectAll"
            :class="isRTL ? ' mr-1' : ' ml-1'"
            @input="checkAllOptions"
        >
        <span>{{ $t("translate.selectAll") }}</span>
      </div>
    </template>
    <template
        v-if="noOptionsText"
        v-slot:no-options="{ search, searching }"
    >
      <template v-if="searching">
        {{ $t("translate.no_result_found") }} <em>{{ search }}</em>.
      </template>
      <em
          v-else
          style="opacity: 0.5"
      >{{ noOptionsText }}</em>
    </template>

    <slot />
  </vue-select>
</template>
<script>
import VueSelect from 'vue-select'

export default {
  name: 'VSelect',
  components: { VueSelect },
  props: {
    multiple: {
      type: Boolean,
      default: false,
    },
    closeOnSelect: {
      type: Boolean,
      default: true,
    },
    clearable: {
      type: Boolean,
      default: true,

    },
    disabled: {
      type: Boolean,
      default: false,

    },
    placeholder: {
      type: String,
      default: '',
    },
    name: {
      type: String,
      default: '',
    },
    options: {
      type: [Array, Object],
      require: true,
      default: () => [],
    },
    label: {
      type: String,
      default: '',
    },
    reduce: {
      type: Function,
      default: undefined,
    },
    noOptionsText: {
      type: String,
      default: '',
    },
    showLoadingIcon: {
      type: Boolean,
      default: false,
    },
    hideSelectAll: {
      type: Boolean,
      default: false,
    },
    getOptionLabel: {
      type: Function,
      default: undefined,
    },

  },
  data() {
    return {
      checkAll: false,
      selected: null,

    }
  },
  computed: {
    closeOnSelect_v() {
      return !this.multiple && this.closeOnSelect
    },
    clearable_v() {
      return this.multiple || this.clearable
    },
    showSelectAll() {
      return !this.hideSelectAll && (this.options.length && this.multiple && this.selected && this.options.length !== this.selected.length)
    },
  },
  watch: {
    selected(val) {
      if (val && this.selected && this.selected.length !== this.options.length) this.checkAll = false
      this.$attrs.value = val
    },
    '$attrs.value': function (val) {
      this.selected = val
    },
  },
  created() {
    this.selected = this.$attrs.value
  },
  methods: {
    onChange() {
      this.$emit('input', this.selected)
      this.$emit('change', this.selected)
    },

    onFocus() {
      this.$emit('v-select-focus')
    },
    onBlur() {
      this.$emit('v-select-blur')
    },
    checkAllOptions() {
      this.checkAll = !this.checkAll
      if (this.checkAll) {
        if (this.reduce !== undefined) this.selected = this.options.map(el => this.reduce(el))
        else this.selected = this.options
      } else this.selected = this.multiple ? [] : null
      this.onChange()
    },

  },
}

</script>

<style lang="scss">
@import "@core/scss/vue/libs/vue-select.scss";
//@import "vue-select/dist/vue-select.css";
.v-select {
  .vs__dropdown-menu {
    overflow-x: hidden;
  }

  .vs__dropdown-option--selected {
    display: none;
  }

  .vs__search {
    $placeholderColor: #999;

    &::placeholder {
      color: $placeholderColor;
    }

    &::-webkit-input-placeholder {
      /* Edge */
      color: $placeholderColor;
    }

    &:-ms-input-placeholder {
      /* Internet Explorer 10-11 */
      color: $placeholderColor;
    }
  }
  .vs__open-indicator {
    margin-top: 0.15rem;
    fill: #aaa;
  }
}
</style>
