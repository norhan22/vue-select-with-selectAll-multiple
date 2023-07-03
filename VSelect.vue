<template>
  <vue-select
    :dir="this.rtl ? 'rtl' : 'ltr'"
    :multiple="multiple"
    :closeOnSelect="closeOnSelect_v"
    :clearable="clearable_v"
    :placeholder="placeholder"
    :name="name"
    :options="options"
    :label="label"
    :reduce="reduce"
    :disabled="disabled"
    v-model="selected"
    :value="selected"
    @input="onChange"
    @search:focus="onFocus"
    @search:blur="onBlur"
    :loading="showLoadingIcon"
  >
    <template #list-header v-if="showSelectAll">
      <input
          type="checkbox"
        v-model="checkAll"
        class="pl-4 pr-0 mb-3 d-block w-auto"
        @change="checkAllOptions"
      />{{ $t('main.selectAll') }}

    </template>
    <template v-slot:no-options="{ search, searching }" v-if="noOptionsText">
      <template v-if="searching">
        {{ $t('basic.no_results_found_for') }} <em>{{ search }}</em
      >.
      </template>
      <em style="opacity: 0.5" v-else>{{ noOptionsText }}</em>
    </template>
    <slot></slot>
  </vue-select>
</template>
<script>
import VueSelect from 'vue-select'

export default {
  name: 'v-select',
  components: {VueSelect},
  props: {
    multiple: {
      type: Boolean,
      default: false
    },
    closeOnSelect: {
      type: Boolean
    },
    clearable: {
      type: Boolean

    },
    disabled: {
      type: Boolean

    },
    placeholder: {
      type: String,
      default: ''
    },
    name: {
      type: String
    },
    options: {
      type: Array,
      require: true
    },
    label: {
      type: String
    },
    reduce: {
      type: Function
    },
    noOptionsText: {
      type: String,
      default: ''
    },
    showLoadingIcon: {
      type: Boolean,
      default: false
    },
    hideSelectAll: {
      type: Boolean,
      default: false
    }

  },
  data () {
    return {
      checkAll: false,
      selected: null

    }
  },
  computed: {
    closeOnSelect_v () {
      return this.closeOnSelect || !this.multiple
    },
    clearable_v () {
      return this.clearable || this.multiple
    },
    showSelectAll () {
      return !this.hideSelectAll && (this.options.length && this.multiple && this.selected && this.options.length !== this.selected.length)
    }
  },
  watch: {
    selected (val) {
      if (val && this.selected && this.selected.length !== this.options.length) this.checkAll = false
      this.$attrs.value = val
    },
    '$attrs.value' (val) {
      this.selected = val

    }
  },
  methods: {
    onChange (e) {
      this.$emit('input', this.selected)
      this.$emit('change', this.selected)
    },

    onFocus () {
      this.$emit('v-select-focus')
    },
    onBlur () {
      this.$emit('v-select-blur')
    },
    checkAllOptions () {
      if (this.checkAll) {
        if (this.reduce !== undefined) this.selected = this.options.map(el => this.reduce(el))
        else this.selected = this.options

      } else this.selected = this.multiple ? [] : null
      this.onChange()
    }

  },
  created () {
    this.selected = this.$attrs.value
  }
}


</script>

<style lang="scss">
@import "@core/scss/vue/libs/vue-select.scss";

.v-select {
  .vs__dropdown-menu {
    overflow-x: hidden;
  }

  .vs__dropdown-option--selected {
    display: none;
  }

  .vs__search {
    $placeholderColor: #ccc;

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
}
</style>
