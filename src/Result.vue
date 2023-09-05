<script>
export default {
  props: ["scores"],
  emits: ["restart"],
  data() {
    return {
      sortOrder: "questionNum",
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
  },
  computed: {
    totalScore() {
      return this.scores.reduce((acc, cur) => acc + this.getScore(cur.targetColour, cur.guessColour), 0);
    },
    sortedScores() {
      switch (this.sortOrder) {
        case "questionNum":
          return this.scores.sort((a, b) => a.questionNum - b.questionNum);
        case "score":
          return this.scores.sort((a, b) => this.getScore(b.targetColour, b.guessColour) - this.getScore(a.targetColour, a.guessColour));
        default:
          return this.scores;
      }
    },
  },
}
</script>

<template>
<h2>Result breakdown</h2>
<span>Total score: {{ totalScore }}</span><br>
<span>Average score: {{ parseFloat((totalScore/scores.length).toFixed(2)) }}</span>
<table class="breakdown">
  <thead>
    <tr>
      <th><button type="button" class="sort-button" @click="sortOrder='questionNum'">#</button></th>
      <th>Target colour</th>
      <th>Your guess</th>
      <th><button type="button" class="sort-button" @click="sortOrder='score'">Score</button></th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="score in sortedScores" :key="score.questionNum">
      <td>{{ score.questionNum }}</td>
      <td><div class="colour-render" :style="{backgroundColor: `rgb${colourString(score.targetColour)}`}"></div><span class="rgb-string">{{ colourString(score.targetColour) }}</span></td>
      <td><div class="colour-render" :style="{backgroundColor: `rgb${colourString(score.guessColour)}`}"></div><span class="rgb-string">{{ colourString(score.guessColour) }}</span></td>
      <td>{{ getScore(score.targetColour, score.guessColour) }}</td>
    </tr>
  </tbody>
</table>
<button type="button" @click="$emit('restart')">Restart</button>
</template>

<style>
.breakdown {
  width: min(32rem, 100%);
  margin: 0.25rem auto;
}
.breakdown th {
  border-bottom: 0.125rem solid;
}
.breakdown td:has(.colour-render) {
  text-align: left;
}
.breakdown .rgb-string {
  display: inline-block;
}
.sort-button {
  margin: 0;
  padding: 0;
  border: none;
  background-color: transparent;
  color: var(--clr-main);
  text-decoration: underline;
}
.sort-button:is(:hover, :focus-visible) {
  text-decoration: none;
}
.colour-render {
  display: inline-block;
  vertical-align: middle;
  margin-right: 0.5em;
  width: 2rem;
  aspect-ratio: 1;
  border: 0.125em solid;
}
</style>