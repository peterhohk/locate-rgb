<script>
export default {
  props: ["initialConfig"],
  emits: ["updateConfig", "start"],
  data() {
    return {
      config: {},
    };
  },
  watch: {
    config: {
      handler(newConfig) {
        if (newConfig.questionTotal < 1) {
          this.config.questionTotal = 1;
        }
        if (newConfig.questionTotal > 99) {
          this.config.questionTotal = 99;
        }
        if (!Number.isInteger(newConfig.questionTotal)) {
          this.config.questionTotal = Math.floor(newConfig.questionTotal);
        }
      },
      deep: true,
    }
  },
  mounted() {
    this.config = this.initialConfig;
  }
}
</script>

<template>
<h2>About</h2>
<table class="explain">
  <tr>
    <th class="red">
      Objective
    </th>
    <td>
      For each question, you'll be shown a randomly-generated colour.<br>
      Estimate the red, green and blue component values of the given colour.
    </td>
  </tr>
  <tr>
    <th class="green">
      Scoring
    </th>
    <td>
      The maximum score for each question is 300.<br>
      Each point of deviation in the red, green or blue component deducts 1 from the score, down to a minimum of 0.<br>
      E.g. if the target colour is <span style="color: rgb(255 166 78)">(255, 166, 78)</span> and your guess is <span style="color: rgb(239 191 31)">(239, 191, 31)</span>, your score would be:<br>
      <ul class="score-computation">
        <li>ΔRed = 255 - 239 = 16</li>
        <li>ΔGreen = 191 - 166 = 25</li>
        <li>ΔBlue = 78 - 31 = 47</li>
        <li>=> Score = 300 - 16 - 25 - 47 = 212</li>
      </ul>
    </td>
  </tr>
  <tr>
    <th class="blue">
      Config
    </th>
    <td>
      <form @change="$emit('updateConfig', config)">
        <label>
          Number of questions: <input type="number" min="1" max="99" v-model="config.questionTotal">
        </label><br>
        <label>
          Time limit:
          <select v-model.number="config.timeLimit">
            <option :value="-1">None</option>
            <option :value="30">30 seconds</option>
            <option :value="20">20 seconds</option>
            <option :value="10">10 seconds</option>
          </select>
        </label>
      </form>
    </td>
  </tr>
</table>
<button type="button" @click="$emit('start')">Start game!</button>
</template>

<style lang="scss">
.explain {
  margin: 0.25rem auto;
  width: 100%;
  th {
    border-right: 0.125rem solid;
    text-decoration: underline;
    rotate: -5deg;
  }
  td {
    font-size: 0.75rem;
    text-align: left;
  }
  .score-computation {
    list-style-type: none;
    padding-left: 1rem;
  }
  @media screen and (min-width: 600px) {
    width: 75%;
  }
}
</style>