<template>
  <div id="app" class="mt-2">
    <div>
      <label>{{ devices.label }}</label>
    </div>
    <select v-model="selectedDevice" name="device">
      <option value disabled></option>
      <option
        v-for="(opt, idx) in devices.identifiers"
        :value="opt.value"
        :key="idx"
      >{{ opt.label }}</option>
    </select>
    <hr>
    <template v-if="hasCategories && selectedDevice">
      <g-select
        :options="optionsForDevice"
        option-label="label"
        name="category"
        :display-mode="categories.display_type"
        :alwaysOpen="shouldAlwaysOpen"
        @input="updateCategories"
        @unselect="unselectCategory"
      ></g-select>
    </template>
    <template v-for="(subSel, idx) in subSelects">
      <div :key="idx">
        <label>{{ subSel.query_label}}</label>
        <g-select
          ref="catSelect" 
          :options="optionsForCategory(subSel)" 
          option-label="label"
          option-value="value"
          :display-mode="subSel.display_type"
          :alwaysOpen="true"
          @input="updateCategories"
          @unselect="unselectCategory"
        ></g-select>
      </div>
    </template>
  </div>
</template>
<script>
/* eslint-disable */
import VueSingleSelect from "./VueSingleSelect.vue";
import GSelect from "./GSelect.vue";
import product from "./product.json";

export default {
  name: "app",
  components: {
    VueSingleSelect,
    GSelect
  },
  data() {
    return {
      selectedDevice: null,
      selectedCategories: [],
      product
    };
  },
  computed: {
    devices() {
      return this.product.material[0];
    },
    categories() {
      return this.product.material[1];
    },
    hasCategories() {
      return this.optionsForDevice.length > 0;
    },
    shouldAlwaysOpen() {
      return this.optionsForDevice.length <= 10;
    },
    optionsForDevice() {
      if (!this.selectedDevice) return [];

      return this.optionsForCategory(this.categories);
    },
    subSelects() {
      return this.selectedCategories
        .filter(cat => cat.composed)
        .reduce((prev, curr) => prev.concat(curr.components), []);
    }
  },
  watch: {
    selectedDevice(curr, prev) {
      // if (this.optionsForDevice.includes(this.selectedCategories)) return;

      this.selectedCategories = this.selectedCategories.filter(cat =>
        this.optionsForDevice.includes(cat)
      );
    },
    selectedCategories(curr, prev) {
      // Treat composed options
    },
    subSelects(curr, prev) {
      // console.log('prev subSelect:', prev)
      // console.log('curr subSelect:', curr)
    }
  },
  methods: {
    optionsForCategory(category) {
      return category.items.filter(cat =>
        cat.identifiers.includes(this.selectedDevice)
      );
    },
    updateCategories(cat, prevCat) {
      if (this.selectedCategories.includes(cat)) return

      this.selectedCategories.push(cat)
      console.log("Adding:", cat)
      this.unselectCategory(prevCat)
    },
    unselectCategory(category) {
      // Remove priview selected Category
      this.selectedCategories = this.selectedCategories.filter((categ) => categ !== category)
      console.log("Removing:", category)
    }
  }
};
</script>

<style>
body {
  font-size: 16px;
}
.w-50 {
  width: 50%;
}
.container {
  width: 1000px;
  margin: 0 auto;
}
.emoji-happy::before {
  content: "\1F600";
}
.emoji-sad::before {
  content: "\1F622";
}
#app {
  font-family: "Oxygen", "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  _font-size: 16px;
}
.pill {
}
</style>
