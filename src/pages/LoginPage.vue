<template>
  <q-page padding class="flex flex-center">
    <q-card flat bordered style="width: 100%; max-width: 400px;">
      <q-card-section class="text-center">
        <div class="text-h5">Iniciar Sesión</div>
      </q-card-section>

      <q-card-section>
        <q-form @submit.prevent="login">
          <q-input
            v-model="email"
            outlined
            label="Correo electrónico"
            type="email"
            lazy-rules
            :rules="[val => !!val || 'El correo es obligatorio']"
            class="q-mb-md"
          />

          <q-input
            v-model="password"
            outlined
            label="Contraseña"
            type="password"
            lazy-rules
            :rules="[val => !!val || 'La contraseña es obligatoria']"
            class="q-mb-md"
          />

          <q-btn
            type="submit"
            color="primary"
            label="Ingresar"
            class="full-width"
            :loading="loading"
          />
        </q-form>

        
        <q-banner
          v-if="error"
          class="bg-red-1 text-red-8 q-mt-md rounded-borders"
          style="border: 1px solid #fecaca"
        >
          {{ error }}
        </q-banner>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useQuasar } from 'quasar'

const $q = useQuasar()
const router = useRouter()

const email = ref('')
const password = ref('')
const loading = ref(false)
const error = ref('')

const login = async () => {
  error.value = ''
  loading.value = true
  

  // login con API 
  try {
    const res = await fetch('https://storedb-api.onrender.com/node-api/users/signin', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        email: email.value,
        password: password.value
      })
    })

    if (res.ok) {
      const data = await res.json()
      console.log('Login exitoso:', data)
      localStorage.setItem('isLoggedIn', 'true')
      router.push('/digimons')
      $q.notify({
        type: 'positive',
        message: '¡Inicio de sesión exitoso!',
        timeout: 2000
      })
    } else {
      const err = await res.json().catch(() => ({}))
      error.value = err.message || 'Credenciales incorrectas'
    }
  } catch (err) {
    console.error('Error de red:', err)
    error.value = 'No se pudo conectar al servidor'
    $q.notify({
      type: 'negative',
      message: 'No se pudo conectar al servidor',
      timeout: 3000
    })
  } finally {
    loading.value = false
  }
}
</script>