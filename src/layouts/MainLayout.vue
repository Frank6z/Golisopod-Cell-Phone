<template>
  <q-layout view="lHh Lpr lFf">
    <!-- Header Principal -->
    <q-header elevated>
      <q-toolbar class="q-py-xs">
        <q-btn 
          flat 
          dense 
          round 
          icon="menu" 
          aria-label="Menu" 
          @click="toggleLeftDrawer" 
        />

        <!-- Logo -->
        <div class="row items-center q-gutter-sm q-ml-sm cursor-pointer" @click="router.push('/')">
          <q-toolbar-title class="text-weight-bold">
            Goli-Cell-Phone
          </q-toolbar-title>
          <img 
            src="../assets/Golicell_Logo.png" 
            alt="Goli-Cell Logo"
            style="height: 50px; width: auto; object-fit: contain;"
          />
        </div>

        <q-space />

        <!-- Barra de Búsqueda Avanzada y Navegación Central -->
        <div class="row items-center q-gutter-md gt-xs position-relative">
          
          <!-- Input + Menú Desplegable flotante -->
          <div class="search-container position-relative">
            <!-- REEMPLAZAR EL <q-input> ACTUAL POR ESTE: -->
            <q-input 
              ref="inputBusquedaRef"
              v-model="textoBusqueda"
              bg-color="blue-grey-4" 
              rounded 
              outlined 
              dense
              clearable
              placeholder="Buscar por nombre o marca..." 
              style="width: 320px;" 
              @focus="mostrarMenuSearch = true"
              @keydown.enter="ejecutarBusqueda(textoBusqueda)"
              @clear="limpiarBusqueda"
            >
              <template #prepend>
                <q-icon 
                  name="search" 
                  class="cursor-pointer" 
                  @click="ejecutarBusqueda(textoBusqueda)" 
                />
              </template>
            </q-input>

            <!-- Cuadro emergente (Sugerencias, Historial y Predicción) -->
            <q-menu 
              v-model="mostrarMenuSearch" 
              no-focus 
              no-parent-event
              fit 
              anchor="bottom left" 
              self="top left"
              class="search-popup-panel"
            >
              <q-card style="width: 580px; max-width: 90vw;" class="q-pa-md bg-white text-grey-9">
                <div class="row q-col-gutter-md">
                  
                  <!-- COLUMNA IZQUIERDA: Sugerencias e Historial -->
                  <div class="col-12 col-md-5 border-right-sm">
                    <div class="text-caption text-bold text-uppercase text-grey-7 q-mb-xs">
                      Sugerencias & Historial
                    </div>
                    
                    <!-- Búsquedas Recientes -->
                    <div v-if="historialBusquedas.length > 0" class="q-mb-sm">
                      <div class="row items-center justify-between q-mb-xs">
                        <span class="text-subtitle2 text-weight-bold">Búsquedas recientes</span>
                        <q-btn 
                          flat 
                          dense 
                          label="Borrar" 
                          size="xs" 
                          color="negative" 
                          @click="limpiarHistorial" 
                        />
                      </div>
                      <q-list dense class="rounded-borders">
                        <q-item 
                          v-for="(term, index) in historialBusquedas" 
                          :key="index"
                          clickable 
                          v-ripple 
                          class="q-px-xs rounded-borders"
                          @click="ejecutarBusqueda(term)"
                        >
                          <q-item-section side class="q-pr-xs">
                            <q-icon name="history" size="xs" color="grey-6" />
                          </q-item-section>
                          <q-item-section class="text-body2 text-grey-8">
                            {{ term }}
                          </q-item-section>
                          <q-item-section side>
                            <q-btn 
                              icon="close" 
                              flat 
                              round 
                              dense 
                              size="xs" 
                              @click.stop="eliminarDelHistorial(index)" 
                            />
                          </q-item-section>
                        </q-item>
                      </q-list>
                    </div>

                    <q-separator class="q-my-xs" v-if="historialBusquedas.length > 0" />

                    <!-- Tags / Marcas populares extraídas dinámicamente -->
                    <div class="text-subtitle2 text-weight-bold q-mt-xs q-mb-xs">Marcas Populares</div>
                    <div class="row q-gutter-xs">
                      <q-chip 
                        v-for="marca in marcasPopulares" 
                        :key="marca"
                        clickable 
                        dense 
                        outline 
                        color="primary" 
                        size="sm"
                        @click="ejecutarBusqueda(marca)"
                      >
                        {{ marca }}
                      </q-chip>
                    </div>
                  </div>

                  <!-- COLUMNA DERECHA: Productos & Predicción del JSON -->
                  <div class="col-12 col-md-7">
                    <div class="row items-center justify-between q-mb-sm">
                      <span class="text-subtitle2 text-weight-bold">Productos</span>
                      <q-btn 
                        flat 
                        dense 
                        no-caps 
                        color="primary" 
                        class="text-weight-bold"
                        @click="ejecutarBusqueda(textoBusqueda)"
                      >
                        Ver todos ({{ productosFiltrados.length }})
                      </q-btn>
                    </div>

                    <!-- Grid de tarjetas predichas (Muestra hasta 3) -->
                    <div v-if="productosFiltrados.length > 0" class="row q-col-gutter-xs">
                      <div 
                        v-for="prod in productosFiltrados.slice(0, 3)" 
                        :key="prod.id" 
                        class="col-4"
                      >
                        <q-card 
                          flat 
                          bordered 
                          class="product-preview-card cursor-pointer q-pa-xs text-center fill-height"
                          @click="irAProducto(prod)"
                        >
                          <q-img 
                            :src="prod.imagen || imagenFallback" 
                            style="height: 65px;" 
                            fit="contain"
                            class="full-width q-mb-xs"
                          />
                          <div class="text-caption text-weight-bold ellipsis-2-lines line-height-tight">
                            {{ prod.nombre }}
                          </div>
                          <div class="text-caption text-primary text-bold q-mt-xs">
                            ${{ prod.precio }}
                          </div>
                        </q-card>
                      </div>
                    </div>

                    <!-- Estado Vacío -->
                    <div v-else class="text-center text-grey-6 q-pa-md">
                      <q-icon name="search_off" size="md" />
                      <div class="text-caption q-mt-xs">No se encontraron productos</div>
                    </div>
                  </div>

                </div>
              </q-card>
            </q-menu>
          </div>

          <q-btn flat icon="home" label="Inicio" to="/" />
          <q-btn flat icon="bar_chart" label="Estadísticas" to="/stats" />
        </div>

        <q-space />

        <!-- Acciones Rápidas (Carrito) -->
        <q-btn 
          round 
          color="purple" 
          glossy 
          icon="local_grocery_store" 
          to="/cart" 
        />           
      </q-toolbar>
    </q-header>

    <!-- Drawer Lateral -->
    <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
      <q-list>
        <q-item-label header>Enlaces Esenciales</q-item-label>
        <EssentialLink 
          v-for="link in linksList" 
          :key="link.label" 
          v-bind="link" 
        />
      </q-list>
    </q-drawer>

    <!-- Contenido dinámico de las rutas -->
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
// 1. ACTUALIZAR LA IMPORTACIÓN EN LA PRIMERA LÍNEA DEL SCRIPT:
import { ref, computed, nextTick } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import EssentialLink from '@/components/EssentialLink.vue'

// Importación directa del archivo JSON de teléfonos
import telephonesData from '@/data/telephones.json'

const router = useRouter()
const route = useRoute()

// 2. AGREGAR ESTA REFERENCIA JUNTO AL ESTADO:
const inputBusquedaRef = ref(null)
const leftDrawerOpen = ref(false)
const textoBusqueda = ref(route.query.search || '')
const mostrarMenuSearch = ref(false)

// Historial de búsquedas del usuario
const historialBusquedas = ref(['Galaxy S25', 'Apple', 'Android'])

// Imagen por defecto si la propiedad "imagen" en el JSON viene vacía
const imagenFallback = 'https://cdn.quasar.dev/img/mobile-logo.png'

// Obtiene automáticamente marcas únicas del JSON para mostrar como Chips rápidos
const marcasPopulares = computed(() => {
  const marcas = telephonesData.map(p => p.marca).filter(Boolean)
  return [...new Set(marcas)].slice(0, 4) // Retorna hasta 4 marcas únicas
})

// Links de Navegación del Sidebar
const linksList = [
  { label: 'Docs', caption: 'quasar.dev', icon: 'school', link: 'https://quasar.dev' },
  { label: 'GitHub', caption: 'github.com/quasarframework', icon: 'code', link: 'https://github.com/quasarframework' },
  { label: 'Discord Chat Channel', caption: 'chat.quasar.dev', icon: 'chat', link: 'https://chat.quasar.dev' },
  { label: 'Forum', caption: 'forum.quasar.dev', icon: 'record_voice_over', link: 'https://forum.quasar.dev' }
]

// Computed: Búsqueda dinámica en tiempo real sobre el JSON importado
const productosFiltrados = computed(() => {
  const query = textoBusqueda.value ? textoBusqueda.value.trim().toLowerCase() : ''
  
  if (!query || query.length < 2) {
    // Si no hay término, mostramos los primeros 3 productos del JSON como sugerencia inicial
    return telephonesData
  }

  // Busca coincidencias tanto en el nombre como en la marca del dispositivo
  return telephonesData.filter(prod => {
    const coincideNombre = prod.nombre?.toLowerCase().includes(query)
    const coincideMarca = prod.marca?.toLowerCase().includes(query)
    return coincideNombre || coincideMarca
  })
})

// Métodos
const toggleLeftDrawer = () => {
  leftDrawerOpen.value = !leftDrawerOpen.value
}

// Ejecuta la búsqueda formal y actualiza los Query Params de la URL
const ejecutarBusqueda = (termino) => {
  const queryLimpia = termino ? termino.trim() : ''
  
  mostrarMenuSearch.value = false

  if (queryLimpia.length >= 2) {
    // Guardar en el historial evitando duplicados
    if (!historialBusquedas.value.includes(queryLimpia)) {
      historialBusquedas.value.unshift(queryLimpia)
      if (historialBusquedas.value.length > 5) historialBusquedas.value.pop()
    }
    
    textoBusqueda.value = queryLimpia
    
    router.push({
      path: '/',
      query: { search: queryLimpia }
    })
  } else {
    router.push({ path: '/', query: { ...route.query, search: undefined } })
  }
}

const limpiarBusqueda = () => {
  textoBusqueda.value = ''
  
  if (route.query.search) {
    router.push({ path: '/', query: { ...route.query, search: undefined } })
  }

  // Mantiene visible el desplegable y regresa el foco al input
  nextTick(() => {
    mostrarMenuSearch.value = true
    inputBusquedaRef.value?.focus()
  })
}

const eliminarDelHistorial = (index) => {
  historialBusquedas.value.splice(index, 1)
}

const limpiarHistorial = () => {
  historialBusquedas.value = []
}

const irAProducto = (producto) => {
  mostrarMenuSearch.value = false
  router.push(`/product/${producto.id}`)
}
</script>

<style scoped>
.border-right-sm {
  border-right: 1px solid #e0e0e0;
}

.line-height-tight {
  line-height: 1.15;
}

.product-preview-card {
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.product-preview-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 10px rgba(0,0,0,0.12);
}
</style>