<template>
  <q-page class="row items-center justify-evenly">
    <q-dialog
      v-model="classDialog"
      persistent
      :maximized="true"
      transition-show="slide-up"
      transition-hide="slide-down"
    >
      <q-card class="bg-dark text-white">
        <q-bar>
          <q-space />
          <q-btn dense flat size="1.2rem" icon="close" v-close-popup>
          </q-btn>
        </q-bar>

        <q-card-section>
          <div style="color: var(--q-primary)" class="text-h3">{{classInfos.name}}</div>
        </q-card-section>

        <q-card-section>
          <div class="text-h5">Proficiency choices</div>
        </q-card-section>
        <q-card-section v-for="item in classInfos.proficiency_choices" :key="item.desc" class="q-pt-none">
          {{item.desc}}
        </q-card-section>

        <q-card-section>
          <div class="text-h5">Proficiencies</div>
        </q-card-section>
        <q-card-section v-for="item in classInfos.proficiencies" :key="item.index" class="q-pt-none">
          {{item.name}}
        </q-card-section>

        <q-card-section>
          <div class="text-h5">Starting Equipment Options</div>
        </q-card-section>
        <q-card-section v-for="item in classInfos.starting_equipment_options" :key="item.desc" class="q-pt-none">
          {{item.desc}}
        </q-card-section>
      </q-card>
    </q-dialog>
      <q-card>
        <q-card-section class="header">
          <img src="src/assets/logo.png" class="logo" alt="logo">
          <img src="src/assets/dragon.gif" class="dragon" alt="logo">
        </q-card-section>
        <q-card-section>
          <q-form
            @submit="onSubmit"
            class="q-gutter-md"
          >
              <q-input
                filled
                v-model="character.name"
                label="Nome do personagem"
              />

            <q-select
              filled
              emit-value
              v-model="character.class"
              :options="classInputOptions"
              label="Class"
              :display-value="capitalizeFirstLetter(character.class)"
              :option-label="option => capitalizeFirstLetter(option)"
            >
              <template v-slot:after>
                <q-btn flat @click="onDetailClass" icon="fa-solid fa-circle-info"></q-btn>
              </template>
            </q-select>
              <q-input
                filled
                v-model="character.background"
                label="Background"
              />
              <q-input
                filled
                v-model="character.playerName"
                label="Player Name"
              />
            <q-input
              filled
              v-model="character.race"
              label="Race"
            />
            <q-input
              filled
              v-model="character.alignment"
              label="Alignment"
            />
            <q-input
              filled
              v-model="character.experiencePoints"
              label="Experience Points"
            />

            <div>
              <q-btn label="Submit" type="submit" color="primary"/>
              <q-btn label="Reset" type="reset" color="primary" flat class="q-ml-sm" />
            </div>
          </q-form>
        </q-card-section>
      </q-card>
  </q-page>
</template>

<script setup lang="ts">
import { reactive, ref, watch } from 'vue'
import { api } from 'boot/axios'
import { capitalizeFirstLetter } from 'src/helpers/general'
const character = reactive({
  name: '',
  class: '',
  race: '',
  background: '',
  playerName: '',
  alignment: '',
  experiencePoints: ''
})

const classInputOptions = [
  'barbarian',
  'bard',
  'cleric',
  'druid',
  'fighter',
  'monk',
  'paladin',
  'ranger',
  'rogue',
  'sorcerer',
  'warlock',
  'wizard'
]

const classInfos = ref(null)

const classDialog = ref(false)

const onSubmit = (callback: SubmitEvent) => {
  callback.preventDefault()
}

const onDetailClass = () => {
  classDialog.value = !!classInfos.value
}

watch(() => character.class, async (newValue) => {
  classInfos.value = (await api.get(`/api/classes/${character.class}`)).data
})

</script>

<style scoped lang="scss">

.header {
  display: flex;
  align-items: flex-end;
  flex-direction: column;
}

.header > .logo {
  width: 70%;
}
.header > .dragon{
  position: absolute;
  height: 70%;
  left: 1rem;
  top: 0
}
.q-card {
  width: 80%;
  max-width: 1024px;
}

@media (max-width: 640px) {
  .header > .logo {
    width: 100%;
  }
  .q-card {
    width: 100%;
  }
}
</style>
