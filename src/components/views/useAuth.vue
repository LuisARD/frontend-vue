<script setup lang="ts">
import { useAuth } from "../features/useAuth";

const {
  activeTab,
  loading,
  errorMessage,
  successMessage,
  loginForm,
  registerForm,
  loggedIn,
  handleLogin,
  handleRegister,
  handleLogout,
} = useAuth();
</script>

<template>
  <div class="auth">
    <h1>Iniciar Sesion del sistema</h1>

    <section class="card" v-if="!loggedIn">
      <div class="tabs">
        <button
          :class="{ active: activeTab === 'login' }"
          @click="activeTab = 'login'"
        >
          Login
        </button>
        <button
          :class="{ active: activeTab === 'register' }"
          @click="activeTab = 'register'"
        >
          Registro
        </button>
      </div>

      <div v-if="activeTab === 'login'">
        <h2>Iniciar sesión</h2>
        <form @submit.prevent="handleLogin">
          <div class="form-row">
            <label>Usuario</label>
            <input v-model="loginForm.username" type="text" required />
          </div>
          <div class="form-row">
            <label>Contraseña</label>
            <input v-model="loginForm.password" type="password" required />
          </div>
          <button type="submit" :disabled="loading">
            {{ loading ? "Cargando..." : "Entrar" }}
          </button>
        </form>
      </div>

      <div v-else>
        <h2>Registrarse</h2>
        <form @submit.prevent="handleRegister">
          <div class="form-row">
            <label>Usuario</label>
            <input v-model="registerForm.username" type="text" required />
          </div>
          <div class="form-row">
            <label>Contraseña</label>
            <input v-model="registerForm.password" type="password" required />
          </div>
          <button type="submit" :disabled="loading">
            {{ loading ? "Cargando..." : "Registrarse" }}
          </button>
        </form>
      </div>

      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
      <p v-if="successMessage" class="success">{{ successMessage }}</p>
    </section>

    <section class="card" v-else>
      <h2>Sesión iniciada</h2>
      <p>Ya tienes un token guardado en el navegador.</p>
      <button @click="handleLogout">Cerrar sesión</button>
    </section>
  </div>
</template>

<style scoped>
.auth {
  max-width: 480px;
  margin: 2rem auto;
  padding: 1.5rem;
  font-family: system-ui, sans-serif;
}

.card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1.25rem;
}

.tabs {
  display: flex;
  gap: 0.75rem;
  margin-bottom: 1rem;
}

.tabs button {
  flex: 1;
  border-radius: 999px;
  border: 1px solid #ccc;
  padding: 0.4rem 0.6rem;
  cursor: pointer;
}

.tabs button.active {
  background-color: #2563eb;
  color: #fff;
  border-color: #2563eb;
}

.form-row {
  display: flex;
  flex-direction: column;
  margin-bottom: 0.75rem;
}

.error {
  color: #b91c1c;
}
.success {
  color: #15803d;
}
</style>
