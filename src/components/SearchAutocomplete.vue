<template>
  <div class="autocomplete">
    <textarea
      ref="input"
      v-model="search"
      @keydown="keyDown"
      @keyup="keyUp"
      type="text"
    />
    <ul v-show="isOpen" class="autocomplete-results">
      <li
        v-for="(result, index) in results"
        :key="index"
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
      results: (Array[String] = []),
      isOpen: false,
      controlPressed: false,
      cursorPosition: 0,
    };
  },
  methods: {
    triggerAutocomplete(e) {
      this.cursorPosition = e.target.selectionStart;
      var portion = this.search.substring(0, this.cursorPosition).toLowerCase();
      if (portion.trim() === "") return;
      var words = portion.split(/\s+/);
      if (words.length == 0) return;
      if (
        !words[words.length - 1].startsWith("{{") &&
        words[words.length - 2] !== "{{"
      )
        return;
      var currentWord = words.slice(-1)[0].trim();
      if (currentWord.startsWith("{{")) {
        currentWord = currentWord.substring(2);
      }
      if (currentWord.includes(".")) {
        var currentSplit = currentWord.split(".");
        var key = currentSplit[0];
        var field = currentSplit[1];
        if (key in this.items) {
          var values = this.items[key].filter(
            (item) => item.toLowerCase().indexOf(field) > -1
          );
          if (values !== undefined) {
            this.results = values.map((item) => key + "." + item);
          }
        }
      } else {
        var keys = this.items.filter((item) =>
          item.toLowerCase().startsWith(currentWord.toLowerCase().trim())
        );
        if (values !== undefined) {
          this.results = keys.flatMap((item) =>
            this.items[key].map((elem) => item + "." + elem)
          );
        }
      }
    },
    keyDown: function (event) {
      if (event.keyCode == 17) {
        this.controlPressed = true;
      }
      if (event.keyCode == 32 && this.controlPressed) {
        this.triggerAutocomplete(event);
        if (this.results.length > 0) {
          this.isOpen = true;
        }
      }
    },
    keyUp: function (event) {
      if (event.keyCode == 17) {
        this.controlPressed = false;
      }
    },
    setResult(result) {
      var portion = this.search.substring(0, this.cursorPosition);
      var words = portion.split(/\s+/);
      var currentWord = words.slice(-1)[0];
      var hasSpaceInFront = true;
      if (currentWord.indexOf("{{") > -1) {
        currentWord = currentWord.substring(currentWord.indexOf("{{") + 2);
        hasSpaceInFront = false;
      }
      var firstPart = this.search.substring(
        0,
        this.cursorPosition - currentWord.length
      );
      var lastPart = this.search.substring(this.cursorPosition);
      if (lastPart.trim().startsWith("}}")) {
        lastPart = lastPart.substring(lastPart.indexOf("}}") + 2);
      }
      this.search = firstPart + result;
      if (hasSpaceInFront) {
        this.search = this.search + " ";
      }
      this.search = this.search + "}}" + lastPart;
      this.isOpen = false;
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

.autocomplete-result.is-selected {
  border-color: #000;
}

.autocomplete-result:hover {
  background-color: #4aae9b;
  color: white;
}
</style>
