<template>
  <div class="appContainer toCenter">
    <SeedForm v-if="!seed"></SeedForm>
    <template v-else>
      <ProposedSolutions
        :seed="seed"
        :maxNumberOfTries="maxNumberOfTries"
        :maxNumberOfInput="maxNumberOfInput"
      ></ProposedSolutions>
      <TotallingModal
        :seed="seed"
        :maxNumberOfTries="maxNumberOfTries"
      ></TotallingModal>
      <CorrectAnswer></CorrectAnswer>
    </template>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue';
import SeedForm from './seed/SeedForm.vue';
import TotallingModal from './totalling/TotallingModal.vue';
import ProposedSolutions from './solution/ProposedSolutions.vue';
import CorrectAnswer from './answer/CorrectAnswer.vue';
import { emitter } from '../../module/emitter';
import axios from 'axios';
import { configApiUrl } from '../../server/module/apiInformation';

export default defineComponent({
  components: {
    SeedForm,
    ProposedSolutions,
    CorrectAnswer,
    TotallingModal,
  },
  setup() {
    const seed = ref<number | null>(null);
    const maxNumberOfInput = ref(0);
    const maxNumberOfTries = ref(0);
    axios
      .post(`${configApiUrl}`)
      .then((response) => {
        maxNumberOfTries.value = response.data.numberleConfig.maxNumberOfTries;
        maxNumberOfInput.value = response.data.numberleConfig.maxNumberOfInput;
      })
      .catch((error) => console.log(error));

    onMounted(() => {
      emitter.on('seedIsSet', (setSeed): void => {
        seed.value = setSeed;
      });
    });

    return {
      seed,
      maxNumberOfTries,
      maxNumberOfInput,
    };
  },
});
</script>

<style lang="scss">
.appContainer {
  height: 100vh;
}
</style>
