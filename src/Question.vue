<script>
function randInt(n) {
  return Math.floor(Math.random() * n);
}
function randomColour() {
  return [randInt(256), randInt(256), randInt(256)];
}

export default {
  props: ["questionNum", "config"],
  emits: ["submitScore", "next", "restart"],
  data() {
    return {
      targetColour: randomColour(),
      guessColour: [0, 0, 0],
      timeRemaining: 0,
      countdownId: 0,
      submitted: false,
    };
  },
  methods: {
    colourString(colour) {
      return `(${colour.join(", ")})`;
    },
    getScore(targetColour, guessColour) {
      const dr = Math.min(Math.abs(targetColour[0] - guessColour[0]), 100);
      const dg = Math.min(Math.abs(targetColour[1] - guessColour[1]), 100);
      const db = Math.min(Math.abs(targetColour[2] - guessColour[2]), 100);
      return 300 - dr - db - dg;
    },
    countdown() {
      this.timeRemaining--;
      if (this.timeRemaining === 0) {
        this.submit();
      }
    },
    submit() {
      clearInterval(this.countdownId);
      this.submitted = true;
      this.$emit("submitScore", {
        questionNum: this.questionNum,
        targetColour: this.targetColour,
        guessColour: this.guessColour,
      });
    },
  },
  mounted() {
    if (this.config.timeLimit !== -1) {
      this.timeRemaining = this.config.timeLimit;
      this.countdownId = setInterval(this.countdown, 1000);
    }
  },
  unmounted() {
    clearInterval(this.countdownId);
  },
}
</script>

<template>
<h2>Question #{{ questionNum }}/{{ config.questionTotal }}</h2>
<span v-if="config.timeLimit!==-1">{{ timeRemaining===0 ? "Time's up!" : `Time: ${timeRemaining}` }}</span>
<div class="question-container">
  <h3 class="target-header">Target colour</h3>
  <div class="target-body" :style="{backgroundColor: `rgb${colourString(targetColour)}`}">
    <span class="target-answer" v-if="submitted">{{ colourString(targetColour) }}</span>
  </div>
  <h3 class="guess-header">Your guess</h3>
  <div class="guess-body" :class="{'before-submit': !submitted, 'after-submit': submitted}">
    <template v-if="!submitted">
      <span class="red">R</span>
      <input type="range" min="0" max="255" v-model.number="guessColour[0]">
      <span>{{ guessColour[0] }}</span>
      <span class="green">G</span>
      <input type="range" min="0" max="255" v-model.number="guessColour[1]">
      <span>{{ guessColour[1] }}</span>
      <span class="blue">B</span>
      <input type="range" min="0" max="255" v-model.number="guessColour[2]">
      <span>{{ guessColour[2] }}</span>
      <button type="button" @click="submit">Submit</button>
    </template>
    <template v-else>
      <div class="guess-render" :style="{backgroundColor: `rgb${colourString(guessColour)}`}"></div>
      <span>{{ colourString(guessColour) }}</span>
      <span>Score: {{ getScore(targetColour, guessColour) }}</span>
      <button type="button" @click="$emit('next')">{{ questionNum===config.questionTotal ? "Finish" : "Next" }}</button>
    </template>
  </div>
</div>
<button type="button" @click="$emit('restart')">Restart</button>
</template>

<style>
.question-container {
  display: grid;
  grid-template-areas:
    "target-header"
    "target-body  "
    "guess-header "
    "guess-body   ";
  grid-template-columns: 1fr;
  gap: 0.25rem 1rem;
  margin: 0.25rem auto;
  max-width: 16rem;
}
.target-header { grid-area: target-header; }
.target-body { grid-area: target-body; }
.guess-header { grid-area: guess-header; }
.guess-body { grid-area: guess-body; }
.target-body {
  position: relative;
  aspect-ratio: 1;
  border: 0.125em solid;
}
.target-answer {
  position: absolute;
  inset: 0;
  margin: auto;
  width: fit-content;
  height: fit-content;
  padding: 0.5rem;
  background-color: var(--clr-bg);
}
.guess-body {
  display: grid;
  align-content: start;
  align-items: center;
  gap: 0.25rem;
  min-height: 12rem;
}
.guess-body.before-submit {
  grid-template-columns: 2rem minmax(0, 1fr) 2rem;
  grid-template-rows: repeat(auto-fit, 2rem);
}
.guess-body.after-submit {
  grid-template-columns: 1fr;
}
.guess-body button {
  margin: auto;
  width: fit-content;
  grid-column: 1 / -1;
}
.guess-render {
  margin: auto;
  width: 100%;
  aspect-ratio: 1;
  border: 0.125em solid;
}
@media (min-width: 36rem) {
  .question-container {
    grid-template-areas:
      "target-header guess-header"
      "target-body   guess-body  ";
    grid-template-columns: repeat(2, minmax(0, 1fr));
    max-width: min(24rem, 75%);
  }
  .guess-render {
    width: 50%;
  }
}
</style>