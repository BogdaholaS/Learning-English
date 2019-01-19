<template>
  <main>
    <h1>{{ $store.state.Words[$store.state.select.arrIndex].section }}</h1>
    <div class="main-btns">
      <button type="button" class="btn btn-danger" @click="back()">
        Back
      </button>
      <button type="button" class="btn btn-success" @click="engToukr()">
        Eng To Ukr
      </button>
      <button type="button" class="btn btn-success" @click="ukrToeng()">
        Ukr To Eng
      </button>
      <button type="button" class="btn btn-success" @click="typeTheWord()">
        Type the word
      </button>
      <button type="button" class="btn btn-success" @click="hearing()">
        Hearing
      </button>
      <button type="button" class="btn btn-success" @click="multipleChoice()">
        Multiple Choice
      </button>
    </div>
    <div class="list" v-if="type === 0">
      <h3>{{ shownWord.eng }}</h3>
      <ul>
        <li v-for="option in finalArray"
            @click="check(option)"
            class="btn btn-warning">
          {{ option.ukr }}
        </li>
      </ul>
    </div>
    <div class="list" v-else-if="type === 1">
      <h3>{{ shownWord.ukr }}</h3>
      <ul>
        <li v-for="option in finalArray"
            @click="check(option)"
            class="btn btn-warning">
          {{ option.eng }}
        </li>
      </ul>
    </div>
    <div class="list" v-else-if="type === 2">
      <h3>{{ shownWord.ukr }}</h3>
      <label for="inp">Type the word here <input type="text" v-model="typedWord" @keyup.enter="confirm()"></label>
    </div>
    <div class="list" v-else-if="type === 3">
      <label for="inp">Type the word here <input type="text" v-model="typedWord" @keyup.enter="confirm()"></label>
    </div>
    <div class="list" v-else="type === 4">
      <div class="mul">
          <ul>
            <li v-for="(option, i) in column.left"
                @click="connect(option, i)">
                <button class="btn btn-warning" :disabled="click === 1">{{ option.eng }}</button>
            </li>
          </ul>
          <ul>
            <li v-for="(option, i) in column.right"
                @click="connect(option, i)">
              <button class="btn btn-warning" :disabled="click === 0">{{ option.ukr }}</button>
            </li>
          </ul>
      </div>
    </div>
    <span>Correct / Incorrect answers - {{ answers.correct }} / {{ answers.incorrect }}</span>
  </main>
</template>

<script>
  export default {
    data: function () {
      return {
        shownWord: '',
        finalArray: [],
        answers: {correct: 0, incorrect: 0},
        type: 0,
        typedWord: '',
        order: Math.round(Math.random() * 3),
        column: {left: [], right: []},
        values: {first: null, second: null, i1: null, i2: null},
        click: 0
      }
    },
    methods: {
      back() {
        this.$store.state.select.isChosen = false;
      },
      test(arr = this.$store.state.Words[this.$store.state.select.arrIndex].words,
           a = Math.floor(Math.random() * arr.length)) {
        this.shownWord = arr[a];
        function random() {
          return Math.random() - 0.5;
        }
        this.finalArray = arr.sort(random).slice(0,4);
        if (!(this.finalArray.includes(this.shownWord))) {
          this.finalArray = arr.slice(0,3);
          this.finalArray.push(this.shownWord);
        }
        this.finalArray.sort(random);
      },
      mul(arr = this.$store.state.Words[this.$store.state.select.arrIndex].words) {
        this.column.left = [];
        this.column.right = [];
        function random() {
          return Math.random() - 0.5;
        }
        let mulArr = arr.sort(random).slice(0,4);
        mulArr.map(item => {
          this.column.left.push(item);
          this.column.right.push(item);
        });
        this.column.left.sort(random);
        this.column.right.sort(random);
      },
      engToukr() {
        this.type = 0;
        this.test();
      },
      ukrToeng() {
        this.type = 1;
        this.test();
      },
      typeTheWord() {
        this.type = 2;
        this.test();
      },
      hearing() {
        this.type = 3;
        this.test();
        responsiveVoice.setDefaultVoice("US English Male");
        responsiveVoice.speak(this.shownWord.eng);
      },
      multipleChoice() {
        this.type = 4;
        this.mul()
      },
      connect(option, i) {
        this.click++;
        if (this.click === 1) {
          this.values.first = option;
          this.values.i1 = i;
        }
        if (this.click === 2) {
          this.values.second = option;
          this.values.i2 = i;
          this.values.first === this.values.second ? this.correct(this.values.i1, this.values.i2) : this.answers.incorrect++;
          this.values.first = null;
          this.values.second = null;
          this.click = 0;
        }
      },
      correct(a, b) {
        this.answers.correct++;
        this.column.left.splice(a, 1);
        this.column.right.splice(b, 1);
        if (this.answers.correct % 4 === 0) {
          this.column.left = [];
          this.column.right = [];
          this.mul()
        }
      },
      confirm() {
        if (this.type === 2) {
          if (this.typedWord === this.shownWord.eng) {
            alert('Correct');
            this.answers.correct++;
          } else {
            alert('Incorrect. The correct answer is ' + this.shownWord.eng + ' - ' + this.shownWord.ukr);
            this.answers.incorrect++;
          }
          this.test();
        }
        else {
          if (this.typedWord === this.shownWord.eng || this.typedWord === this.shownWord.ukr) {
            alert('Correct');
            this.answers.correct++;
          } else {
            alert('Incorrect. The correct answer is ' + this.shownWord.eng + ' - ' + this.shownWord.ukr);
            this.answers.incorrect++;
          }
          this.test();
          responsiveVoice.speak(this.shownWord.eng);
        }
        this.typedWord = '';
      },
      check(option) {
        if (option === this.shownWord) {
          alert('Correct');
          this.answers.correct++;
        } else {
          alert('Incorrect. The correct answer is ' + this.shownWord.eng + ' - ' + this.shownWord.ukr);
          this.answers.incorrect++;
        }
        this.test();
        this.options = [];
      }
    }
  }
</script>

<style lang="scss">
  main {
    text-align: center;
  }
  .main-btns button{
    margin-top: 40px;
  }
  h3{
    margin: 20px;
  }
  ul {
    padding: 0;
    list-style: none;
    display: flex;
    justify-content: center;
    li{
      margin: 20px;
      display: flex;
      justify-content: center;
      align-items: center;
      button {
        min-width: 200px;
      }
    }
  }
  button {
    margin: 0 10px;
  }
  label {
    margin-top: 10px;
  }
  .mul {
    display: flex;
  }
  .mul {
    display: flex;
    justify-content: center;
    ul {
      display: flex;
      flex-direction: column;
    }
  }
</style>
