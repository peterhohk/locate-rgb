<script>
export default {
  props: ["scores"],
  emits: ["restart"],
  methods: {
    colourString(colour) {
      return `(${colour.join(", ")})`
    },
    getScore(targetColour, guessColour) {
      const dr = Math.abs(targetColour[0] - guessColour[0]);
      const dg = Math.abs(targetColour[1] - guessColour[1]);
      const db = Math.abs(targetColour[2] - guessColour[2]);
      return Math.max(500 - dr - db - dg, 0);
    },
  },
  computed: {
    totalScore() {
      return this.scores.reduce((acc, cur) => acc + this.getScore(cur.targetColour, cur.guessColour), 0);
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
      <th>#</th>
      <th>Target colour</th>
      <th>Your guess</th>
      <th>Score</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="score in scores" :key="score.questionNum">
      <td>{{ score.questionNum }}</td>
      <td><div class="colour-render" :style="{backgroundColor: `rgb${colourString(score.targetColour)}`}"></div>{{ colourString(score.targetColour) }}</td>
      <td><div class="colour-render" :style="{backgroundColor: `rgb${colourString(score.guessColour)}`}"></div>{{ colourString(score.guessColour) }}</td>
      <td>{{ getScore(score.targetColour, score.guessColour) }}</td>
    </tr>
  </tbody>
</table>
<button type="button" @click="$emit('restart')">Restart</button>
</template>

<style lang="scss">
.breakdown {
  width: min(864px, 100%);
  margin: 0.25rem auto;
  th {
    border-bottom: 0.125rem solid;
  }
  td:has(.colour-render) {
    text-align: left;
  }
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