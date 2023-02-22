<template>
  <q-layout view="hHr lpR fFr">

    <q-header class="bg-primary text-white">
      <q-toolbar>
        <q-toolbar-title>
          <q-avatar>
            <q-icon name="fa-solid fa-dragon"></q-icon>
          </q-avatar>
        </q-toolbar-title>

        <q-btn dense flat round icon="menu" @click="toggleRightDrawer" />
      </q-toolbar>
    </q-header>

    <q-drawer show-if-above v-model="leftDrawerOpen" side="right">
      <q-scroll-area class="fit">
        <q-list>

          <template v-for="(menuItem, index) in menuList" :key="index">
            <q-item @click="router.push(menuItem.route)" clickable :active="menuItem.label === 'Outbox'" v-ripple>
              <q-item-section avatar>
                <q-icon :name="menuItem.icon" />
              </q-item-section>
              <q-item-section>
                {{ menuItem.label }}
              </q-item-section>
            </q-item>
            <q-separator :key="'sep' + index"  v-if="menuItem.separator" />
          </template>

        </q-list>
      </q-scroll-area>
    </q-drawer>

    <q-page-container>
      <q-page class="row items-center justify-evenly">
        <q-card>
      <router-view />
        </q-card>
      </q-page>
    </q-page-container>

  </q-layout>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const leftDrawerOpen = ref(false)
const menuList = [
  {
    icon: 'home',
    label: 'Home',
    separator: true,
    route: '/'
  },
  {
    icon: 'rocket',
    label: 'Spells',
    separator: false,
    route: '/spells-helper'
  },
  {
    icon: 'person',
    label: 'Class Wiki',
    separator: false,
    route: '/class-wiki'
  },
  {
    icon: 'help',
    label: 'Help',
    separator: false,
    route: '/help'
  }
]
function toggleRightDrawer () {
  leftDrawerOpen.value = !leftDrawerOpen.value
}
</script>

<style scoped lang="scss">

.q-card {
  width: 80%;
  max-width: 1024px;
}

@media (max-width: 940px) {
  .q-card {
    width: 100%;
  }
}

@media (max-width: 640px) {
  .header > .logo {
    width: 100%;
  }

}
</style>
