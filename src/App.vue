<script>
import Intro from "./Intro.vue";
import Question from "./Question.vue";
import Result from "./Result.vue";

export default {
  components: {
    Intro,
    Question,
    Result,
  },
  data() {
    return {
      activeTheme: sessionStorage.getItem("activeTheme") || "dark",
      config: {
        questionTotal: 10,
        timeLimit: -1,
      },
      state: "intro",
      questionNum: 0,
      scores: [],
    };
  },
  watch: {
    activeTheme(newActiveTheme) {
      document.body.className = newActiveTheme;
      sessionStorage.setItem("activeTheme", newActiveTheme);
    }
  },
  methods: {
    updateConfig(newConfig) {
      this.config = newConfig;
    },
    start() {
      this.state = "question";
      this.questionNum = 1;
    },
    addScore(score) {
      this.scores.push(score);
    },
    next() {
      if (this.questionNum === this.config.questionTotal) {
        this.state = "result";
      }
      this.questionNum++;
    },
    restart() {
      this.state = "intro";
      this.questionNum = 0;
      this.scores = [];
    },
  },
  mounted() {
    document.body.className = this.activeTheme;
  },
};
</script>

<template>
<header :class="{ expanded: !(state==='question') }">
  <h1>Locate<span class="red">R</span><span class="green">G</span><span class="blue">B</span></h1>
  <template v-if="state==='intro'">
    <p>Test your familiarity with the RGB colour space</p>
  </template>
  <div class="theme-switcher">
    <span>Theme</span>
    <input type="checkbox" name="theme-toggle" class="theme-toggle" checked v-model="activeTheme" true-value="dark" false-value="light">
  </div>
</header>
<main>
  <template v-if="state==='intro'">
    <Intro
      :initial-config="config"
      @update-config="updateConfig"
      @start="start"
    />
  </template>
  <template v-else-if="state==='question'">
    <template v-for="i in config.questionTotal" :key="i">
      <template v-if="questionNum===i">
        <Question
          :question-num="i"
          :config="config"
          @submit-score="addScore"
          @next="next"
          @restart="restart"
        />
      </template>
    </template>
  </template>
  <template v-else>
    <Result
      :scores="scores"
      @restart="restart"
    />
  </template>
</main>
<footer>
  (C) 2023 <a href="https://github.com/peterhohk" target="_blank">peterhohk</a>
</footer>
</template>

<style>
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
button, input, select {
  font: inherit;
}
button, label, select, [type="checkbox"], [type="range"] {
  cursor: pointer;
}
table {
  border-collapse: collapse;
  border-spacing: 0;
}
/* RESET ENDS */
/* GENERAL STYLE STARTS */
:root {
  font-size: clamp(1rem, 3vw, 2rem);
}
body {
  margin-bottom: 1rem;
  overflow-y: scroll;
  background-color: var(--clr-bg);
  text-align: center;
  font-family: Poppins, sans-serif;
  color: var(--clr-main);
  line-height: 1.25;
}
header {
  margin-bottom: 1rem;
}
header h1 {
  font-size: 2rem;
  transition: font-size 0.4s ease-in-out;
}
header.expanded h1 {
  font-size: 3rem;
}
h2 {
  font-size: 1.25rem;
}
h3 {
  font-size: 1rem;
  text-decoration: underline;
}
a:where(:link, :visited) {
  color: var(--clr-accent);
}
a:where(:hover, :focus-visible) {
  color: var(--clr-main);
  text-shadow: 0 0 0.0625em;
}
button {
  padding: 0.125em 0.5em;
  margin: 0.25rem;
  border: 0.125rem solid var(--clr-main);
  border-radius: 1rem;
  background-color: var(--clr-bg);
  color: var(--clr-main);
  font-weight: bold;
}
button:where(:hover, :focus-visible) {
  background-color: var(--clr-accent);
  color: var(--clr-bg);
}
[type="number"], select {
  padding: 0 0.125em;
  border: none;
  background-color: transparent;
  color: var(--clr-main);
  border-bottom: 0.0625em solid;
}
option {
  color: initial;
}
th, td {
  padding: 0.25rem;
}
.red {
  color: #ff0000;
}
.green {
  color: #00ff00;
}
.blue {
  color: #0000ff;
}
footer {
  margin-right: 1em;
  font-size: 0.5rem;
  text-align: right;
}
/* GENERAL STYLE ENDS */
/* THEME SWITCHER STYLE STARTS */
body.light {
  --clr-bg: #ffffff;
  --clr-main: #000000;
  --clr-accent: #7f7f7f;
}
body.dark {
  --clr-bg: #000000;
  --clr-main: #ffffff;
  --clr-accent: #bfbfbf;
}
.theme-toggle {
  appearance: none;
  position: relative;
  margin-left: 0.25rem;
  width: 2rem;
  height: 1rem;
  border: 0.125rem solid var(--clr-main);
  border-radius: 0.5rem;
  vertical-align: middle;
}
.theme-toggle::after {
  display: block;
  content: "";
  position: absolute;
  inset: 0.0625rem 1.0625rem 0.0625rem 0.0625rem;
  border-radius: 50%;
  background-color: var(--clr-accent);
  transition: inset 0.4s ease-in-out;
}
.theme-toggle:is(:hover, :focus-visible)::after {
  background-color: var(--clr-main);
}
.theme-toggle:checked::after {
  inset: 0.0625rem 0.0625rem 0.0625rem 1.0625rem;
}
@media (min-width: 36rem) {
  .theme-switcher {
    position: absolute;
    top: 0.5rem;
    right: 1rem;
    width: min-content;
  }
  .theme-toggle {
    margin-left: 0;
  }
}
/* THEME SWITCHER STYLE ENDS */
</style>