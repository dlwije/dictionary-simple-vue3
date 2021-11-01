<template>
  <div id="appContainer">
    <form @submit.prevent="getDefinitions">
      <input
        type="text"
        placeholder="Word to search..."
        id="word"
        v-model="word"
      />
      <button class="btn waves-effect waves-light" type="submit">
        <i class="material-icons right">Get Definition</i>
      </button>
    </form>
    <p v-if="!validQuery">
      Error: The word you are searching for does not exist. Please try with a
      different word.
    </p>
    <h5 v-if="word && word !== ''">Definitions for {{ word }} :</h5>
    <div
      id="cardContainer"
      v-for="(definition, index) in definitionArray"
      :key="index"
    >
      <div class="row">
        <div class="col s12 m5">
          <div class="card-panel teal">
            <span class="white-text">{{ definition }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { ref } from "vue";
export default {
  name: "App",
  setup() {
    let validQuery = ref(true);
    const definitionArray = ref([]);
    const word = ref("");

    const getDefinitions = async () => {
      /* clear the array before feed new data */
      definitionArray.value.splice(0, definitionArray.value.length);
      try {
        /* calling to api for take the word result */
        const url =
          "https://api.dictionaryapi.dev/api/v2/entries/en/" + word.value;

        const { data } = await axios.get(url);

        /* the result map into array */
        data.map(({ meanings }) => {
          meanings.map(({ definitions }) => {
            definitions.forEach(({ definition }) => {
              if (typeof definition === "string")
                definitionArray.value.push(definition);
            });
          });
        });
        validQuery.value = true;
      } catch (error) {
        validQuery.value = false;
      }
    };

    return { word, getDefinitions, definitionArray, validQuery };
  },
};
</script>

<style>
#appContainer {
  margin: 10px;
}
</style>
