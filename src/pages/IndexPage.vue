<template>
  <q-page class="q-pa-md">
    <div class="row q-col-gutter-md items-center q-mb-lg bg-grey-2 q-pa-md rounded-borders">
      
      <div class="col-xs-12 col-sm-6 col-md-4 row q-col-gutter-sm items-center">
        <div class="col-6">
          <q-input dense outlined type="number" v-model.number="filtroPrecioMin" label="Precio Mínimo" prefix="$" clearable />
        </div>
        <div class="col-6">
          <q-input dense outlined type="number" v-model.number="filtroPrecioMax" label="Precio Máximo" prefix="$" clearable />
        </div>
      </div>

      <div class="col-xs-12 col-sm-6 col-md-4">
        <q-btn-group unelevated class="full-width">
          <q-btn dense class="col" color="primary" label="Precio" :icon="ordenPrecio === 'asc' ? 'arrow_upward' : 'arrow_downward'" @click="toggleOrdenPrecio" />
          <q-btn dense size="sm" color="secondary" icon="clear" @click="ordenPrecio = null" title="Quitar orden de precio"
          />
        </q-btn-group>
      </div>

      <div class="col-xs-12 col-sm-6 col-md-4">
        <q-btn-group unelevated class="full-width">
          <q-btn dense class="col" color="primary" label="Fecha Salida" :icon="ordenFecha === 'asc' ? 'arrow_upward' : 'arrow_downward'" @click="toggleOrdenFecha" />
          <q-btn dense size="sm" color="secondary" icon="clear" @click="ordenFecha = null" title="Quitar orden de fecha"/>
        </q-btn-group>
      </div>

    </div>

    <div class="row q-col-gutter-md q-mt-md">
      <div v-for="tel in telefonosFiltradosYOrdenados" :key="tel.id" class="col-xs-12 col-sm-6 col-md-4">
        <q-card class="my-card column justify-between" style="height: 100%;">
          
          <q-img :src="tel.imagen || 'https://cdn.quasar.dev/img/parallax2.jpg'" height="200px" >
            <div class="absolute-top-right bg-secondary text-white q-pa-xs rounded-borders">
              {{ tel.marca }}
            </div>
          </q-img>

          <q-card-section>
            <div class="text-h6 text-weight-bold">{{ tel.nombre }}</div>
            
            <div class="text-subtitle2 text-grey-8 q-mt-xs">
              Sistema: <span class="text-weight-bold text-primary">{{ tel.sistema }}</span>
            </div>
            
            <div class="text-subtitle2 text-grey-8">
              Pantalla: {{ tel.pantalla }}" | Salida: {{ tel.fechaSalida }}
            </div>

            <div class="text-h5 text-weight-bold text-accent q-mt-md">
              ${{ tel.precio }}
            </div>
          </q-card-section>

          <q-card-actions align="right">
            <q-btn flat color="primary" icon="add_shopping_cart" label="Añadir al Carrito" />
          </q-card-actions>

        </q-card>
      </div>
    </div>

  </q-page>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router'
import listaTelefonosData from '../data/telephones.json'

const route = useRoute()
const filtroPrecioMin = ref(null)
const filtroPrecioMax = ref(null)
const ordenPrecio = ref(null)
const ordenFecha = ref(null)

const listaTelefonos = ref(listaTelefonosData)

const toggleOrdenPrecio = () => {
  ordenPrecio.value = ordenPrecio.value === 'asc' ? 'desc' : 'asc'
}

const toggleOrdenFecha = () => {
  ordenFecha.value = ordenFecha.value === 'asc' ? 'desc' : 'asc'
}

// FILTROS
const telefonosFiltradosYOrdenados = computed(() => {
  let resultado = [...listaTelefonos.value]

  const textoQuery = (route.query.search || '').toLowerCase()
  if (textoQuery) {
    resultado = resultado.filter(t => 
      t.nombre.toLowerCase().includes(textoQuery) ||
      t.marca.toLowerCase().includes(textoQuery) ||
      t.sistema.toLowerCase().includes(textoQuery)
    )
  }

  if (filtroPrecioMin.value !== null && filtroPrecioMin.value !== '') {
    resultado = resultado.filter(t => t.precio >= filtroPrecioMin.value)
  }

  if (filtroPrecioMax.value !== null && filtroPrecioMax.value !== '') {
    resultado = resultado.filter(t => t.precio <= filtroPrecioMax.value)
  }

  if (ordenPrecio.value) {
    resultado.sort((a, b) => {
      return ordenPrecio.value === 'asc' ? a.precio - b.precio : b.precio - a.precio
    })
  }

  if (ordenFecha.value) {
    resultado.sort((a, b) => {
      const fechaA = new Date(a.fechaSalida)
      const fechaB = new Date(b.fechaSalida)
      return ordenFecha.value === 'asc' ? fechaA - fechaB : fechaB - fechaA
    })
  }

  return resultado
})
</script>