<template>
  <div class="p-2 space-y-4">
    <div class="text-2xl">Players</div>
    <div>
      <button @click="addPlayer" class="border p-1 rounded shadow px-4">
        Add Player
      </button>
    </div>
    <div class="grid grid-cols-2 gap-2">
      <template v-for="player in players">
        <input v-model="player.name" class="border indent-2"/>
      </template>
    </div>
    <div>
      <div class="text-2xl py-4 border-t">
        Results
      </div>
      <div class="px-2">
        <div v-for="entity in entities" class="flex space-x-2">
          <div class="font-bold">
            {{ entity.name }}:
          </div>
          <div>
            <div v-if="entity.result" class="text-red-500">
              {{ entity.result }}
            </div>
            <div v-else class="text-gray-500">
              Unknown
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-for="entity in entities">
      <div class="text-2xl py-4 border-t" @click="entity.visible = !entity.visible">
        {{ entity.name }}
      </div>
      <div class="px-2" v-if="entity.visible">
        <template v-for="item in entity.items">
          <div class="font-bold">
            {{ item.name }}
          </div>
          <div class="p-2">
            <div>Owner</div>
            <select class="border" v-model="item.owner" @change="changeOwner(item)">
              <option :value="undefined">
                Unkown
              </option>
              <option v-for="player in players" :value="player.name">
                {{ player.name }}
              </option>
            </select>
          </div>
          <div class="p-2" v-if="!item.owner">
            <div>Doen't have</div>
            <template v-for="player in players">
              <div class="space-x-2">
                <input type="checkbox" :id="item.name + player.name" :value="player.name"
                       @change="changeNotHave(entity, item, player, $event)"/>
                <label :for="item.name + player.name">{{ player.name }}</label>
              </div>
            </template>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const players = ref([
  { name: "Myself" },
])

const entities = ref([
      {
        name: "Suspect",
        visible: false,
        items: [
          { name: "Sargento Bigode" },
          { name: "Florista Dona Branca" },
          { name: "Advogado senhor Marinho" },
          { name: "Cheff de cozinha Tonny Gourmet" },
          { name: "Dançarina senhorita Rosa" },
          { name: "Médica Dona Violeta" },
          { name: "Coveiro Sérgio Soturno" },
          { name: "Mordomo James" },]
      },
      {
        name: "Weapon",
        visible: false,
        items: [
          { name: "Faca" },
          { name: "Soco inglês" },
          { name: "Pé de cabra" },
          { name: "Tesoura" },
          { name: "Pá" },
          { name: "Arma química" },
          { name: "Veneno" },
          { name: "Espingarda" },
        ]
      },
      {
        name: "Location",
        visible: false,
        items: [
          { name: "Restaurante" },
          { name: "Prefeitura" },
          { name: "Banco" },
          { name: "Hospital" },
          { name: "Floricultura" },
          { name: "Praça central" },
          { name: "Mansão" },
          { name: "Hotel" },
          { name: "Cemitério" },
          { name: "Estação de trem" },
          { name: "Boate" },
        ]
      },
    ]
)

const addPlayer = () => {
  players.value.push({
    name: "New player",
  })
}

const changeOwner = (item) => {
  item.notOwners = []
  recalculate();
}

const changeNotHave = (entity, item, player, event) => {
  const checked = event.target.checked
  item.notOwners = item.notOwners || []

  if (!checked) {
    item.notOwners = item.notOwners.filter((name) => name !== player.name)
  } else {
    item.notOwners.push(player.name)
  }

  recalculate();
}

const recalculate = () => {
  for (const entity of entities.value) {
    for (const item of entity.items) {
      if (!item.owner && item.notOwners && item.notOwners.length === players.value.length) {
        entity.result = item.name
        break;
      } else {
        entity.result = null
      }
    }
  }
}
</script>
