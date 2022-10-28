<template>
  <q-page class="row items-center justify-evenly">
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
          <q-select
            filled
            emit-value
            v-model="character.class"
            :options="classInputOptions"
            label="Class"
            :display-value="capitalizeFirstLetter(character.class)"
            :option-label="option => capitalizeFirstLetter(option)"
          />
          <div v-for="item in spellsInfos" :key="item.index">
            <q-card-section>
              <div class="text-h6">{{item.name}}</div>
            </q-card-section>
            <q-card-section class="q-pt-none">
              <q-btn v-if="!item.spell" @click="getSpell(item.url, item.index)" style="width: 100%" flat  icon="fa-solid fa-caret-down"></q-btn>
              <div v-else>
                <div v-for="desc in item.spell.desc" :key="desc">
                  {{desc}}
                </div>
              </div>
            </q-card-section>
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

const spellsInfos = ref<Record<string, any>[]>([])

const getSpell = async (url:string, index: string) => {
  const result = (await api.get(url)).data
  spellsInfos.value = spellsInfos.value.map((item) => {
    if (item.index === index) {
      return { ...item, spell: result }
    } else {
      return item
    }
  })
}

const onSubmit = (callback: SubmitEvent) => {
  callback.preventDefault()
  classDialog.value = !!classInfos.value
}

watch(() => character.class, async () => {
  debugger
  const requestResult = await api.get(`/api/classes/${character.class}/spells`)
  const data = requestResult.data
  spellsInfos.value = data.results
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
