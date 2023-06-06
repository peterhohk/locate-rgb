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
    console.log("I see you, dirty cheater ;)");
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
    <input type="checkbox" id="theme-toggle" checked v-model="activeTheme" true-value="dark" false-value="light">
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
  ©️ 2023 <a href="https://github.com/peterhohk" target="_blank">peterhohk</a>
</footer>
</template>

<style lang="scss">
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
button, input, select {
  font: inherit;
  line-height: inherit;
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
  font-size: clamp(16px, 3vw, 32px);
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
  h1 {
    font-size: 2rem;
    transition: font-size 0.4s ease-in-out;
  }
  &.expanded h1 {
    font-size: 3rem;
  }
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
a:where(:hover, :focus-visible, :active) {
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
  &:where(:hover, :focus-visible) {
    background-color: var(--clr-accent);
    color: var(--clr-bg);
  }
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
  font-size: 0.5rem;
  text-align: right;
}
/* GENERAL STYLE ENDS */
/* SCROLLBAR STYLE STARTS */
::-webkit-scrollbar {
  background-color: transparent;
  width: 6px;
  height: 6px;
}
::-webkit-scrollbar-thumb {
  background-color: var(--clr-main);
}
/* SCROLLBAR STYLE ENDS */
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
.theme-switcher {
  @media screen and (min-width: 600px) {
    position: absolute;
    top: 0.5rem;
    right: 1rem;
    width: min-content;
  }
}
#theme-toggle {
  appearance: none;
  position: relative;
  margin-left: 0.25rem;
  width: 2rem;
  height: 1rem;
  border: 0.125rem solid var(--clr-main);
  border-radius: 0.5rem;
  vertical-align: middle;
  &::after {
    display: block;
    content: "";
    position: absolute;
    inset: 0.0625rem 1.0625rem 0.0625rem 0.0625rem;
    border-radius: 50%;
    background-color: var(--clr-accent);
    transition: inset 0.4s ease-in-out;
  }
  &:is(:hover, :focus-visible)::after {
    background-color: var(--clr-main);
  }
  &:checked::after {
    inset: 0.0625rem 0.0625rem 0.0625rem 1.0625rem;
  }
  @media screen and (min-width: 600px) {
    margin-left: 0;
  }
}
/* THEME SWITCHER STYLE ENDS */
</style>