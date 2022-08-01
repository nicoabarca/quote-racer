<script>
import "../assets/quote-racer.css";
export default {
  data() {
    return {
      textInput: "",
      wordsList: [],
      quoteAuthor: "",
      activeWord: "",
      activeIndex: 0,
      correctWords: 0,
    };
  },
  created() {
    this.fetchRandomQuote();
  },
  computed: {
    inputDisabled() {
      return this.activeIndex === this.wordsList.length ? true : false;
    },
    inputPlaceholder() {
      return this.activeIndex === 0 ? "Write here!" : "";
    },
  },
  methods: {
    checkWord() {
      this.wordsList[this.activeIndex] === this.textInput.trim()
        ? (this.$refs.words[this.activeIndex].className = "correct")
        : (this.$refs.words[this.activeIndex].className = "incorrect");

      this.changeActiveWord();
      this.textInput = "";
    },

    changeActiveWord() {
      this.activeIndex += 1;
      if (!(this.activeIndex >= this.wordsList.length)) {
        this.activeWord = this.wordsList[this.activeIndex];
        this.highlightActiveWord();
      }
    },

    highlightActiveWord() {
      this.$refs.words[this.activeIndex].className = "highlighted";
    },

    async fetchRandomQuote() {
      const url = "https://api.quotable.io/random";
      await (await fetch(url)).json().then((data) => {
        this.resetRace(data);
      });
    },

    resetRace(newData) {
      this.wordsList = newData["content"].split(" ");
      this.quoteAuthor = newData["author"];
      this.activeWord = this.wordsList[0];
      this.activeIndex = 0;
      if (this.$refs.words) {
        this.$refs.words.forEach((el) => (el.className = "neutral"));
      }
    },
  },
};
</script>

<template>
  <div class="main">
    <div class="game">
      <div class="word-counter">{{ activeIndex }} / {{ wordsList.length }}</div>
      <div v-if="wordsList" class="text">
        <span
          v-for="(word, index) in wordsList"
          :key="index"
          :id="index"
          ref="words"
          class="neutral"
        >
          {{ `${word} ` }}
        </span>
      </div>
      <p class="author">
        Author: <i>{{ quoteAuthor }}</i>
      </p>
      <input
        class="text-input"
        autofocus
        maxlength="16"
        type="text"
        v-model="textInput"
        @keydown.space="checkWord"
        :disabled="inputDisabled"
        :placeholder="inputPlaceholder"
      />
      <div>
        <button class="reset-button" @click="fetchRandomQuote">
          Reset Quote
        </button>
      </div>
    </div>
  </div>
</template>
