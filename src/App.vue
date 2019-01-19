<template>
  <div>
    <h1 v-if="!this.$store.state.select.isChosen">Choose section! ({{sum}})</h1>
    <Words v-if="this.$store.state.select.isChosen"></Words >
    <ul class="buttons" v-else>
      <li
        class="btn btn-warning"
        v-for="(section, index) in sections"
        @click="onChoose(index)">
        {{ section }} ({{ arrlength[index] }})
      </li>
    </ul>
  </div>
</template>

<script>
  import Words from "./components/Words.vue";
  export default {
    data: function () {
      return {
        sections: [],
        arrlength: [],
        sum: 0
      }
    },
    created() {
      this.$store.state.Words.map(item => {
        this.sections.push(item.section);
        this.sum += item.words.length;
        this.arrlength.push(item.words.length);
      });
    },
    methods: {
      onChoose(index) {
        this.$store.state.select.isChosen = true;
        this.$store.state.select.arrIndex = index;
      }
    },
    components: {
      Words
    }
  }
</script>

<style>
  body {
    font-family: 'Roboto', sans-serif;
    background: lightgreen;
  }
  h1 {
    margin: 20px;
    text-align: center;
  }
  .buttons {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
  }
  .buttons button {
    margin: 10px;
  }
</style>
