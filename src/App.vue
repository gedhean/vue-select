<template>
  <div id="app" class="mt-2">
    <div>
      <label>{{ devices.query_label }}</label>
    </div>
    <!-- Device select -->
    <select v-model="selectedDevice" name="device">
      <option value></option>
      <option v-for="(opt, idx) in devices.filters" :value="opt.device" :key="idx">{{ opt.labels }}</option>
    </select>
    <hr>
    <!-- Category select -->
    <div
      v-if="hasCategories && selectedDevice"
      v-show="optionsForCategory(categories.items).length > 1"
    >
      <label>{{ categories.query_label }}</label>
      <g-select
        name="category"
        option-label="label"
        :getOptionValue="getOptionValue()"
        :options="optionsForCategory(categories.items)"
        :alwaysOpen="shouldAlwaysOpen"
        @input="updateCategories"
        @unselect="unselectCategory"
        :value="optionsForCategory(categories.items).length == 1 ? optionsForCategory(categories.items)[0] : null"
      ></g-select>
    </div>
    <!-- Subcategories selects -->
    <template v-for="(subCateg, idx) in subCategories">
      <div
        :key="idx"
        v-if="optionsForCategory(subCateg.items).length"
        v-show="optionsForCategory(subCateg.items).length > 1"
      >
        <label>{{ subCateg.query_label}}</label>
        <g-select
          ref="subCategories"
          option-label="label"
          :options="optionsForCategory(subCateg.items)"
          :getOptionValue="getOptionValue()"
          :alwaysOpen="true"
          @unselect="unselectCategory"
          @input="updateCategories"
          :value="optionsForCategory(subCateg.items).length == 1 ? optionsForCategory(subCateg.items)[0] : null"
        ></g-select>
        <!-- @hook:beforeDestroy="unselectCategory" -->
      </div>
    </template>
    <!-- Addons -->
    <hr>
    <template v-if="addons.items.length">
      <label>{{ addons.query_label }}</label>
      <div v-for="(addon, idx) in addons.items" :key="idx">
        <label>{{ addon.query_label }}</label>
        <g-select 
          :options="addon.items"
          :alwaysOpen="true"
          @unselect="unselectAddons"
          @input="updateAddons"
        />
      </div>
    </template>
    <hr>
    <template v-if="customization">
      <label>Customizations</label>
      <!-- needd a label -->
      <div v-for="(value, key) in customization.properties.keys" :key="key">
        <label>{{key}}</label>
        <input v-model="customizations[key]" :type="customization.type" :pattern="customization.properties.regexp" :maxlength="customization.properties.maxlength">
      </div>
    </template>
  </div>
</template>
<script>
/* eslint-disable */
import VueSingleSelect from "./VueSingleSelect.vue";
import GSelect from "./GSelect.vue";
import productMock from "./productV2.json";

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
      selectedAddons: [],
      customizations: {},
      product: productMock.product
    };
  },
  computed: {
    devices() {
      return this.product[0];
    },
    categories() {
      return this.product[1];
    },
    hasCategories() {
      return this.optionsForDevice.length > 0;
    },
    shouldAlwaysOpen() {
      return this.optionsForDevice.length <= 10;
    },
    optionsForDevice() {
      return this.optionsForCategory(this.categories.items);
    },
    subCategories() {
      // This doesn't works with 3 levels nested components
      // composed1 > composed2 > children
      // When composed1 is removed/unselected the children of composed2
      // Remain in the UI. Must refactore this shit!
      return this.selectedCategories
        .filter(cat => cat.composed)
        .reduce((prev, curr) => prev.concat(curr.components), []);
    },
    lineItemsSlugs() {
      return this.selectedCategories
        .filter(cat => cat && !cat.composed)
        .map(cat => cat.slugs[this.selectedDevice]);
    },
    addons() {
      return this.product[2]
    },
    customization() {
      return productMock.customization
    }
  },
  watch: {
    selectedDevice(curr, prev) {
      // if (this.optionsForDevice.includes(this.selectedCategories)) return;

      /* this.selectedCategories = this.selectedCategories.filter(cat =>
        this.optionsForDevice.includes(cat)
      ); */
      this.selectedCategories = [];
    },
    selectedCategories(curr, prev) {
      // Treat composed options
      // let diff = prev.filter(el => !curr.includes(el));
      // console.log("diff: ", diff);

      // if (diff.length) {
      //   diff.forEach(el => {
      //     if (el.composed) {
      //       el.components.forEach(child => {
      //         this.selectedCategories = this.selectedCategories.filter(cat => cat != child);
      //       });
      //     }
      //   });
      // }
    },
    subCategories(curr, prev) {
      // console.log('prev subSelect:', prev)
      // console.log('curr subSelect:', curr)
    }
  },
  methods: {
    optionsForCategory(options) {
      return options.filter(option =>
        option.filters.includes(this.selectedDevice)
      );
    },
    updateCategories(cat, prevCat) {
      if (
        cat === null ||
        cat === undefined ||
        this.selectedCategories.includes(cat)
      ) {
        return;
      }

      this.selectedCategories.push(cat);
      this.unselectCategory(prevCat);

      // console.log("Adding:", cat);
    },
    unselectCategory(category) {
      // Remove priview selected Category
      this.selectedCategories = this.selectedCategories.filter(
        categ => categ != category
      );

      // console.log("Removing:", category);
    },
    updateAddons(addon, prevAddon) {
      if (
        addon === null ||
        addon === undefined ||
        this.selectedAddons.includes(addon)
      ) {
        return;
      }

      this.selectedAddons.push(addon);
      this.unselectAddon(prevAddon);
    },
    unselectAddons(addon) {
      this.selectedAddons = this.selectedAddons.filter(add => add != addon)
    },
    getOptionValue() {
      const device = this.selectedDevice;

      return option => option && option.slugs && option.slugs[device];
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
