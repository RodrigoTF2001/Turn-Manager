<template>
  <v-container class="fill-height">
    <v-responsive class="align-center fill-height mx-auto" max-width="900">
      <h1 class="mb-10">Turn Manager RPG</h1>
      <div class="input-section">
        <v-text-field
          v-model="name"
          hide-details="auto"
          label="Nome"
          class="mb-5"
        ></v-text-field>

        <v-text-field
          v-model="initiative"
          class="mb-5"
          label="Iniciativa"
          type="number"
        ></v-text-field>

        <v-btn
          :disabled="!name || !initiative"
          variant="outlined"
          style="background-color: red; color: black; height: 55px"
          @click="addTurn"
          class="mb-5"
        >
          Adicionar
        </v-btn>
      </div>

      <div v-if="turns.length > 0">
        <h2 class="mt-5">Turno: {{ turnNumber }}</h2>
        <v-row justify="center" class="nav-buttons">
          <v-btn class="mr-5" @click="previousTurn">⬅</v-btn>
          <v-btn @click="nextTurn">➡</v-btn>
        </v-row>

        <v-table height="300px" fixed-header>
          <thead>
            <tr>
              <th class="text-left">Nome</th>
              <th class="text-left">Iniciativa</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(item, index) in turns"
              :key="index"
              :class="{ 'current-turn': index === currentTurnIndex }"
            >
              <td>{{ item.name }}</td>
              <td>{{ item.initiative }}</td>
            </tr>
          </tbody>
        </v-table>

        <center>
          <v-btn
            variant="outlined"
            style="background-color: red; color: black; height: 55px"
            class="mt-5"
            @click="clearTurns"
          >
            Encerrar Combate
          </v-btn>
        </center>
      </div>
    </v-responsive>
  </v-container>
</template>

<script setup>
import { ref } from "vue";

// State
const name = ref("");
const initiative = ref("");
const turns = ref([]);
const turnNumber = ref(1);
const currentTurnIndex = ref(0);

// Methods
const addTurn = () => {
  if (name.value && initiative.value) {
    turns.value.push({
      name: name.value,
      initiative: parseInt(initiative.value, 10),
    });
    sortTurns();
    name.value = "";
    initiative.value = "";
  }
};

const sortTurns = () => {
  turns.value.sort((a, b) => b.initiative - a.initiative);
};

const clearTurns = () => {
  turns.value = [];
  turnNumber.value = 1;
  currentTurnIndex.value = 0;
};

const nextTurn = () => {
  if (turns.value.length > 0) {
    currentTurnIndex.value++;
    if (currentTurnIndex.value >= turns.value.length) {
      currentTurnIndex.value = 0;
      turnNumber.value++;
    }
  }
};

const previousTurn = () => {
  if (turns.value.length > 0) {
    currentTurnIndex.value--;
    if (currentTurnIndex.value < 0) {
      currentTurnIndex.value = turns.value.length - 1;
      turnNumber.value = Math.max(turnNumber.value - 1, 1);
    }
  }
};
</script>

<style scoped>
.nav-buttons {
  margin-bottom: 20px;
}

.current-turn {
  background-color: #bf2222;
}

@media only screen and (max-width: 600px) {
  .input-section {
    padding: 0 20px;
  }

  .nav-buttons {
    flex-direction: row;
    justify-content: space-between;
    margin-bottom: 15px;
    margin-top: 18px;
  }

  .v-table__overflow {
    overflow-x: auto;
  }
}
</style>
