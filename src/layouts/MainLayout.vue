<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn flat dense round icon="menu" aria-label="Menu" @click="toggleLeftDrawer" />
          <div class="row items-center q-gutter-sm">
             <q-toolbar-title class="text-weight-bold"> Goli-Cell-Phone </q-toolbar-title>
             <img src="../assets/Golicell_Logo.png" height="100px" width=auto fit="contain"/>
          </div>
          <q-space />
          <div class="row items-center q-gutter-md gt-xs">
            <q-input bg-color="blue-grey-4" rounded outlined v-model="textoBusqueda" label="Buscar" style="width: 220px;" @update:model-value="filtrarTelefonos">
              <template #prepend><q-icon name="search"/></template>
            </q-input>
            <q-btn flat label="Inicio" icon="home" to="/" />
            <q-btn flat label="Estadísticas" icon="bar_chart" to="/stats" />
          </div>
          <q-space />
          <q-btn round color="purple" glossy icon="local_grocery_store" to="/cart" />           
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
      <q-list>
        <q-item-label header> Essential Links </q-item-label>

        <EssentialLink v-for="link in linksList" :key="link.label" v-bind="link" />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import EssentialLink from '@/components/EssentialLink.vue'

const router = useRouter()
const route = useRoute()
const textoBusqueda = ref(route.query.search || '')

const filtrarTelefonos = (val) => {
  router.push({
    path: '/',
    query: { search: val ? val : undefined }
  })
}


const linksList = [
  {
    label: 'Docs',
    caption: 'quasar.dev',
    icon: 'school',
    link: 'https://quasar.dev',
  },
  {
    label: 'GitHub',
    caption: 'github.com/quasarframework',
    icon: 'code',
    link: 'https://github.com/quasarframework',
  },
  {
    label: 'Discord Chat Channel',
    caption: 'chat.quasar.dev',
    icon: 'chat',
    link: 'https://chat.quasar.dev',
  },
  {
    label: 'Forum',
    caption: 'forum.quasar.dev',
    icon: 'record_voice_over',
    link: 'https://forum.quasar.dev',
  },
  {
    label: 'Twitter',
    caption: '@quasarframework',
    icon: 'rss_feed',
    link: 'https://twitter.quasar.dev',
  },
  {
    label: 'Facebook',
    caption: '@QuasarFramework',
    icon: 'public',
    link: 'https://facebook.quasar.dev',
  },
  {
    label: 'Quasar Awesome',
    caption: 'Community Quasar projects',
    icon: 'favorite',
    link: 'https://awesome.quasar.dev',
  },
]

const leftDrawerOpen = ref(false)

function toggleLeftDrawer() {
  leftDrawerOpen.value = !leftDrawerOpen.value
}
</script>
