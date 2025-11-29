<template>
  <q-page padding>
    <div class="text-h5 q-mb-md">Lista de Digimons</div>

    
    <div class="row q-col-gutter-md q-mb-lg">
      <div class="col-12 col-md-6">
        <q-input
          v-model="searchName"
          outlined
          label="Buscar por nombre"
          placeholder="Escribe un nombre..."
          dense
        />
      </div>
      <div class="col-12 col-md-6">
        <q-select
          v-model="searchLevel"
          :options="levels"
          outlined
          label="Filtrar por nivel"
          placeholder="Todos los niveles"
          dense
          clearable
        />
      </div>
    </div>

    
    <div v-if="loading" class="text-center q-mt-xl">
      <q-spinner size="40px" color="primary" />
      <div class="q-mt-md">Cargando Digimons...</div>
    </div>

    <div v-else-if="filteredDigimons.length > 0" class="row q-col-gutter-md">
      <div
        v-for="digimon in filteredDigimons"
        :key="digimon.name"
        class="col-12 col-sm-6 col-md-4 col-lg-3"
      >
        <q-card flat bordered class="q-pa-md text-center">
          <q-img
            :src="digimon.img || 'https://via.placeholder.com/200x200?text=Digimon'"
            style="height: 200px; object-fit: contain"
            class="q-mb-md"
          />
          <div class="text-h6">{{ digimon.name }}</div>
          <div class="text-caption text-primary">
            Nivel: {{ digimon.level || 'Desconocido' }}
          </div>
        </q-card>
      </div>
    </div>

    <div v-else class="q-pa-md bg-grey-2 text-center rounded-borders">
      No se encontraron Digimons.
    </div>
  </q-page>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const digimons = ref([])
const loading = ref(true)
const searchName = ref('')
const searchLevel = ref(null)


const levels = computed(() => {
  const unique = [...new Set(digimons.value.map(d => d.level).filter(Boolean))]
  return unique.sort()
})


const filteredDigimons = computed(() => {
  return digimons.value.filter(d => {
    const matchesName = d.name.toLowerCase().includes(searchName.value.toLowerCase())
    const matchesLevel = !searchLevel.value || d.level === searchLevel.value
    return matchesName && matchesLevel
  })
})


onMounted(async () => {
  try {
    const res = await fetch('https://digimon-api.vercel.app/api/digimon')
    if (res.ok) {
      digimons.value = await res.json()
    } else {
      console.error('Error al cargar Digimons')
    }
  } catch (err) {
    console.error('Error de red:', err)
  } finally {
    loading.value = false
  }
})
</script>