<template>
  <q-card-section class="select-class">
    <q-select
      filled
      emit-value
      v-model="selectedClass"
      :options="classInputOptions"
      label="Class"
      :display-value="capitalizeFirstLetter(`${selectedClass || 'Select a class'}`)"
      :option-label="option => capitalizeFirstLetter(option)"
    />
<!--    <q-select
      filled
      emit-value
      :options="['spells', 'proficiencies', 'features', 'subclasses']"
      v-model="infoToShow"
      @update="onClickShowInfo(infoToShow, selectedClass)"
      label="Class"
      :display-value="capitalizeFirstLetter(`${infoToShow || 'Select an option'}`)"
      :option-label="option => capitalizeFirstLetter(option)"
    />-->
    <div class="actions">
      <q-btn color="red" @click="onClickShowInfo('spells', selectedClass)" label="Spells"></q-btn>
      <q-btn color="purple" @click="onClickShowInfo('subclasses', selectedClass)" label="SubClasses"></q-btn>
      <q-btn color="green" @click="onClickShowInfo('features', selectedClass)" label="Features"></q-btn>
      <q-btn color="grey" @click="onClickShowInfo('proficiencies', selectedClass)" label="Proficiences"></q-btn>
    </div>
    <q-card-section v-if="infoToShow !== 'none'" class="infos">
      <div class="header">
        <h3><b>{{capitalizeFirstLetter(infoToShow)}}</b></h3>
        <q-input filled v-model="searchWord" label="Search" >
          <template v-slot:append>
            <q-icon name="search"></q-icon>
          </template>
        </q-input>
      </div>
      <q-btn icon="fa-solid fa-plus" :label="info.name" v-for="info in filterSearch(infos[infoToShow], searchWord)" :key="info.name"/>
    </q-card-section>
  </q-card-section>

</template>

<script lang="ts" setup>

import { reactive, ref } from 'vue'
import { capitalizeFirstLetter } from 'src/helpers/general'
import { api } from 'boot/axios'

enum classList {
  barbarian,
  bard,
  cleric,
  druid,
  fighter,
  monk,
  paladin,
  ranger,
  rogue,
  sorcerer,
  warlock,
  wizard
}

/* INTERFACES */

type infoType = {
  index: string
  name: string
  url: string
}

interface infosInterface {
  spells: infoType[]
  proficiencies: infoType[]
  features: infoType[]
  subclasses: infoType[]
}

const onClickShowInfo = async (infoType:keyof infosInterface, className:classList) => {
  infos[infoType] = await getInfoByClass(infoType, className)
  infoToShow.value = infoType
}

const infoToShow = ref<keyof infosInterface | 'none'>('none')

const getInfoByClass = async (infoType:keyof infosInterface, className:classList): Promise<infoType[]> => {
  const { data: { results } } = await api.get(`/api/classes/${className}/${infoType}`)
  return results
}

const searchWord = ref<string>('')

const filterSearch = (infos: infoType[], search: string) => {
  if (!search.length) return infos
  return infos.filter(info => info.name.toLowerCase().includes(search.toLowerCase()))
}

const infos = reactive<infosInterface>({
  spells: [],
  proficiencies: [],
  features: [],
  subclasses: []
})

const selectedClass = ref<classList>()

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

</script>

<style lang="scss">

.header {
  margin: 10px 0;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  width: 100%;
}

.infos {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.actions {
  display: flex;
  justify-content: center;
  margin: 20px 0 0 0;
}

.actions > .q-btn {
  margin: 0 2px;
}

@media (max-width: 640px) {
  .header {
    flex-direction: column;
    padding: 0 0 30px 0;
  }
  .actions {
    flex-direction: column;
    align-items: center;
  }

  .actions > .q-btn {
    width: 200px;
    margin: 2px 0;
  }
}

</style>
