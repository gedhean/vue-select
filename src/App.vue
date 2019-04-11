<template>
  <div id="app" class="mt-2">
    <vue-single-select
      v-model="thread"
      :options="threads"
      :required="true"
      option-label="a_title"
      :getOptionDescription="getCustomDescription"
      option-key="id"
      name="dude"
    ></vue-single-select>
    <hr>
    <g-select 
        v-model="thread2" 
        :options="threads"
        option-label="a_title"
        option-key="id"
        placeholder="Query select..."
        name="deedee"
    ></g-select>
  </div>
</template>
<script>
/* eslint-disable */

// import "./../public/app.css";
// import VueTaggablepleSelect from 'vue-simple-taggable-select';
import VueSingleSelect from "./VueSingleSelect.vue";
import GSelect from "./GSelect.vue";
export default {
  name: "app",
  created() {
    //this.numbers = _.range(20,40);
    //this.thread = this.threads[1]
  },
  methods: {
    getCustomDescription(opt) {
      return opt.a_title;
    },

    addOption(opt) {
      //this.myThreads.push(opt);
      //this.threads.push(opt);
      this.fruits.push(opt);
    },
    zed(opt) {
      return opt + " fruit";
    }
  },
  mounted() {
    //let r = _.sample(this.fruits)
    //this.fruit = 'peach'
    setTimeout(() => {
      /* let threads = _.sampleSize(this.threads, 2);
       * this.myThreads = [this.threads[0]];*/
    }, 2000);
    setInterval(() => {
      //      this.fruit = _.sample(this.fruits)
      //this.myThreads = [];
    }, 1000);
  },
  components: {
    VueSingleSelect,
    GSelect
  },
  data() {
    return {
      nums: [],
      numbers: [],
      fruit: "apple",
      veggies: [],
      fruits: [
        "apple",
        "cherry",
        "pear",
        "peach",
        "banana",
        "orange",
        "plum",
        "grape"
      ],
      myThreads: [],
      thread: "",
      thread2: "",
      threads: [
        { id: 1, a_title: "baz bar" },
        { id: 2, a_title: "foo bar" },
        {
          id: 3,
          a_title: "Eos rerum veniam quia mollitia"
        },
        { id: 4, a_title: "Robs Thread" },
        { id: 5, a_title: "test" },
        { id: 6, a_title: "goose" },
        { id: 7, a_title: "loose goose" },
        { id: 8, a_title: "geese" },
        { id: 9, a_title: "moose" },
        { id: 10, a_title: "test thread updated" },
        {
          id: 11,
          a_title:
            "Distinctio quo praesentium quis commodi praesentium excepturi."
        },
        { id: 12, a_title: "changed new thread" },
        { id: 13, a_title: "fred" },
        { id: 14, a_title: "barney" },
        { id: 15, a_title: "fredn and barney" }
      ]
    };
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
