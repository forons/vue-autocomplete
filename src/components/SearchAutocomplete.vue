<template>
  <div class="autocomplete">
    <input v-model="search" @input="onChange" type="text" />
    <ul v-show="isOpen" class="autocomplete-results">
      <li
        v-for="(result, i) in results"
        :key="i"
        @click="setResult(result)"
        class="autocomplete-result"
      >
        {{ result }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "SearchAutocomplete",
  props: {
    items: {
      type: Object[(String, Array[String])],
      required: false,
      default: () => [],
    },
  },
  data() {
    return {
      search: "",
      results: [],
      isOpen: false,
    };
  },
  methods: {
    setResult(result) {
      this.search = result;
      this.isOpen = false;
    },
    filterResults() {
      if (this.search.indexOf(".") < -1) {
        return;
      }
      var elems = this.search.toLowerCase().split(".");
      var key = elems[0];
      var field = elems[1];
      if (key in this.items) {
        var values = this.items[key].filter(
          (item) => item.toLowerCase().indexOf(field) > -1
        );
        this.results = values.array.forEach((item) => key + "." + item);
      }
    },
    onChange() {
      this.filterResults();
      if (this.results.length > 0) {
        this.isOpen = true;
      }
    },
  },
};
</script>
<style>
.autocomplete {
  border: 1px solid #000;
  border-radius: 0.4rem;
  padding: 0.1rem 0.3rem;
  box-decoration-break: clone;

  position: relative;
}

.autocomplete-results {
  padding: 0;
  margin: 0;
  border: 1px solid #eeeeee;
  height: 120px;
  min-height: 1em;
  max-height: 6em;
  overflow: auto;
}

.autocomplete-result {
  list-style: none;
  text-align: left;
  padding: 4px 2px;
  cursor: pointer;
}

.autocomplete-result:hover {
  background-color: #4aae9b;
  color: white;
}
</style>
