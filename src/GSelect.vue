<template>
	<div class="g-select-root">
    <div class="g-select-wrapper">
      <div class="g-select-inline-block">
        <input 
          type="text" 
          class="g-select-search-ipunt" 
          readonly="readonly" 
          autocomplete="off" 
          :placeholder="placeholder"
          @click="toggleDropDown"
          @keyup.esc.stop="closeOut"
          :value="selectedOption ? getOptionDescription(selectedOption) : null"
        >
      </div>
      <ul v-if="dropDownOpen" :style="{'max-height': maxHeight}">
        <li 
          v-for="(item, idx) in options" 
          :key="idx"
          @mouseover="setPointerIndex(idx)"
          @click.prevent="setOption()"
        >
          {{ item[optionLabel] }}
        </li>
      </ul>
      <div v-if="selectedOption">
        <input type="hidden" :name="name" ref="selectedValue" :value="getOptionValue(selectedOption)">
      </div>
    </div>
	</div>
</template>
<script>
export default {
  props: {
    value: {
      required: true
    },
    name: {
      type: String,
      required: false,
      default: () => ""
    },
    options: {
      type: Array,
      required: false,
      default: () =>  []
    },
    optionLabel: {
      type: String,
      required: false,
      default: () => null
    },
    optionKey: {
      type: String,
      required: false,
      default: () => null
    },
    placeholder: {
      type: String,
      required: false,
      default: () => null
    },
    maxHeight: {
      type: String,
      default: () => "220px",
      required: false
    },
    getOptionDescription: {
      type: Function,
      default: function(option) {
        if (this.optionKey && this.optionLabel) {
          return option[this.optionKey] + " " + option[this.optionLabel];
        }
        if (this.optionLabel) {
          return option[this.optionLabel];
        }
        if (this.optionKey) {
          return option[this.optionKey];
        }
        return option;
      }
    },
    getOptionValue: {
      type: Function,
      default: function(option) {
        if (this.optionKey) {
          return option[this.optionKey];
        }

        if (this.optionLabel) {
          return option[this.optionLabel];
        }

        return option;
      }
    },
  },
  mounted() {
    document.addEventListener("click", this.handleClickOutside)

    if (this.value && this.options.includes(this.value)) {
      this.selectedOption = this.value
    }
  },
  destroyed() {
    document.removeEventListener("click", this.handleClickOutside)
  },
  data() {
    return {
      selectedOption: null,
      dropDownOpen: false,
      pointer: -1
    }
  },
  watch: {
    value(curr, prev) {
      this.selectedOption = curr
    },
    selectedOption(curr, prev) {
      this.$emit("input", curr);
    }
  },
  methods: {
    setPointerIndex(index) {
      this.pointer = index
    },
    toggleDropDown() {
      this.dropDownOpen = !this.dropDownOpen
    },
    handleClickOutside(e) {
      if (this.$el.contains(e.target)) return;

      this.dropDownOpen = false
    },
    openDropDown(e) {
      this.dropDownOpen = true
    },
    closeDropDown(e) {
      this.dropDownOpen = false
    },
    setOption(e) {
      this.selectedOption = this.options[this.pointer]
      this.dropDownOpen = false
    },
    closeOut(e) {
      console.log(e)
      this.closeDropDown()
      this.selectedOption = null
    }
  }
};
</script>
<style scoped>
input {
  width: 100%;
  padding: 8px;

}

ul {
  padding-left: 0px;
  overflow: auto;
}

ul > li {
  list-style: none;
}

li {
  padding: 0.5em 0.75em;
}

li:hover {
  background-color: #999;
  color: #FFF;
}
</style>