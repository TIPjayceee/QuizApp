<template>
  <section class="home">
    <v-card class="center">
    <h1>Midterm Project Quiz App</h1>

  
    <p>Choose difficulty</p>
    <div class="difficulties">
      <div>
        <input
          @click="changeDifficulty($event)"
          name="difficulty"
          type="radio"
          id="easy"
        /><label class="button" for="easy" role="button">Easy</label>
      </div>
      <div>
        <input
          @click="changeDifficulty($event)"
          name="difficulty"
          type="radio"
          id="medium"
          checked
        /><label class="button" for="medium" role="button">Medium</label>
      </div>
      <div>
        <input
          @click="changeDifficulty($event)"
          name="difficulty"
          type="radio"
          id="hard"
        /><label class="button" for="hard" role="button">Hard</label>
      </div>
    </div>

    <p>Input how many items (min of 10, max of 50)</p>
    <input
      class="questions"
      type="number"
      min="10"
      max="50"
      v-model="questions"
    /><br>

    <button @click="passEvent()">Start!</button>
    </v-card>
  </section>
</template>

<script>
import axios from "../services/axios";

export default {
  name: "Home",
  data() {
    return {
      categories: [],
      selectedCategoryId: 31,
      difficulty: "medium",
      questions: 10,
    };
  },

  async mounted() {
    try {
      const { data } = await axios.get("https://opentdb.com/api.php?amount=10&category=31");
      this.categories = data.trivia_categories;
      this.selectedCategoryId = data.trivia_categories[31].id;
    } catch (error) {
      console.error(error);
    }
  },

  methods: {
    changeCategory(event) {
      const options = event.target.options;
      const index = options.selectedIndex;

      if (index > -1) {
        this.selectedCategoryId = options[index].id;
      }
    },
    changeDifficulty(event) {
      this.difficulty = event.target.id;
    },
    passEvent() {
      this.$emit("startTheGame", {
        difficulty: this.difficulty,
        categoryId: this.selectedCategoryId,
        questions: this.questions,
      });
    },
  },
};
</script>

<style lang="scss" scoped>
@use "../App.scss";
.home {
  width: min(95%, 400px);
  @include App.flex();
  
  .center{
    width: 200%;
    height: 450px;
    padding: 60px;
    background-color: #D53326;
   text-align: center; 
  }

  h1 {
    font-size: 2.5rem;
  }

  p {
    text-align: center;
    margin: 10px 0 5px;
    font-size: 1.3rem;
  }

  select {
    width: 80%;
    padding: 3px;
    font-size: 1rem;
    cursor: pointer;

    &:focus {
      outline: 0;
    }
  }

  .difficulties {
    @include App.flex(row);
    gap: 7px;

    div {
      @include App.flex(row);
      position: relative;
      margin-bottom: 5px;
      input {
        position: absolute;
        opacity: 0;
        cursor: pointer;
        height: 0;
        width: 0;
        background: white;
        &:checked ~ .button {
          background: App.$difficulty-checked;
          box-shadow: App.$box-shadow;
        }
      }
      .button {
        transition: 0.3s;
        display: inline-block;
        width: 70px;
        text-align: center;
        color: black;
        background: App.$difficulty-btn;
      }
      &:hover input:not(:checked) ~ .button {
        background: App.$difficulty-hover;
      }
    }
  }

  .questions {
    width: 25%;
    padding: 2px 5px;
 
  }
  input{
    background: white;
  }
}
</style>