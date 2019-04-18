<template>
	<div class="g-select-root">
    <div class="g-select-wrapper">
      <div class="g-select-inline-block" v-if="true || !alwaysOpen">
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
          v-for="(option, idx) in options" 
          :key="idx"
          @mouseover="setPointerIndex(idx)"
          @click.prevent="setOption()"
          :class="{selected: option == selectedOption, compact: option.display_type == 'bullet'}"
        > 
          <g-option-thumb 
            v-if="option.display_type == 'image'" 
            :label="getOptionDescription(option)"
            :imageSrc="option.display_resource"
          ></g-option-thumb>
          <g-option-bullet 
            v-else-if="option.display_type == 'color'" 
            :color="option.display_resource"
          ></g-option-bullet>
          <slot 
            v-else 
            name="option" 
            v-bind="{option,idx}"
          >{{ getOptionDescription(option) }}</slot>
        </li>
      </ul>
      <div v-if="selectedOption">
        <input type="hidden" :name="name" ref="selectedValue" :value="getOptionValue(selectedOption)">
      </div>
    </div>
	</div>
</template>
<script>
import GOptionThumb from "./GOptionThumb.vue"
import GOptionBullet from "./GOptionBullet.vue"

export default {

  props: {
    value: {
      required: false
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
    // displayMode: {
    //   validator: (value) => ['text','color', 'image'].includes(value),
    //   default: () => 'text',
    //   required: false
    // },
    maxHeight: {
      type: String,
      default: () => "220px",
      required: false
    },
    alwaysOpen: {
      type: Boolean,
      default: false,
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
    document.addEventListener("keyup", this.handleClickOutside)

    this.dropDownOpen = this.alwaysOpen

    if (this.value && this.options.includes(this.value)) {
      this.selectedOption = this.value
    }
  },
  beforeDestroy() {
    this.$emit('unselect', this.selectedOption)
    console.log('emitting unselect: ', this.selectedOption);
  },
  destroyed() {
    document.removeEventListener("click", this.handleClickOutside)
    document.removeEventListener("keyup", this.handleClickOutside)
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
      this.$emit("input", curr, prev);
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

      this.closeDropDown()
    },
    openDropDown(e) {
      if (this.alwaysOpen) return;
      this.dropDownOpen = true
    },
    closeDropDown(e) {
      if (this.alwaysOpen) return;
      this.dropDownOpen = false
    },
    setOption(e) {
      this.selectedOption = this.options[this.pointer]
      this.closeDropDown()
    },
    closeOut(e) {
      console.log(e)
      this.closeDropDown()
      this.selectedOption = null
    }
  },
  components: {
    GOptionThumb,
    GOptionBullet
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
  display: inline-block;
  width: 160px;
  max-height: 100px
}

li {
  padding: 0.4em 0.75em;
}

li:hover {
  background-color: #999;
  color: #FFF;
}

.selected {
  border: #999 1px solid;
}

.compact {
  width: auto;
}
</style>