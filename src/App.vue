<template>
  <div id="app">
    <SearchAutocomplete
      :items="{
        company: [
          'Apple',
          'Banana',
          'Orange',
          'Mango',
          'Pear',
          'Peach',
          'Grape',
          'Tangerine',
          'Pineapple',
        ],
        other: ['val1', 'val2'],
      }"
    />
  </div>
</template>

<script>
import SearchAutocomplete from "./components/SearchAutocomplete.vue";
export default {
  name: "App",
  components: {
    SearchAutocomplete,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
