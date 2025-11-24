<!--Este parte sirve mas que nada para el navbar   -->
<script setup lang="ts">
import { computed } from "vue";
import { useRouter } from "vue-router";
import { isLoggedIn, logout } from "./services/authService";

const router = useRouter();

const loggedIn = computed(() => isLoggedIn());

function handleLogout() {
  logout();
  router.push("/login");
}
</script>

<template>
  <div class="app-root">
    <!-- Barra de navegación -->
    <nav class="navbar">
      <div class="nav-left">
        <span class="brand">Gestión Productos & Ventas</span>

        <router-link to="/productos" class="nav-link">
          Productos
        </router-link>

        <!-- Clientes y Ventas solo si está logueado -->
        <router-link
          v-if="loggedIn"
          to="/clientes"
          class="nav-link"
        >
          Clientes
        </router-link>

        <router-link
          v-if="loggedIn"
          to="/ventas"
          class="nav-link"
        >
          Ventas
        </router-link>
      </div>

      <div class="nav-right">
        <!-- Si NO está logueado, mostrar botón para ir a login -->
        <router-link
          v-if="!loggedIn"
          to="/login"
          class="nav-link nav-auth"
        >
          Login / Registro
        </router-link>

        <!-- Si está logueado, mostrar botón de logout -->
        <button
          v-else
          class="nav-button-logout"
          @click="handleLogout"
        >
          Cerrar sesión
        </button>
      </div>
    </nav>

    <!-- Contenido de cada página -->
    <main class="main-content">
      <router-view />
    </main>
  </div>
</template>

<style scoped>
.app-root {
  min-height: 100vh;
  background: #f3f4f6;
}

/* Navbar */
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.6rem 1.25rem;
  background-color: #111827;
  color: #f9fafb;
}

.nav-left {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.brand {
  font-weight: 600;
  margin-right: 1rem;
}

.nav-link {
  text-decoration: none;
  color: #e5e7eb;
  font-size: 0.95rem;
  padding: 0.25rem 0.5rem;
  border-radius: 999px;
}

.nav-link.router-link-active {
  background-color: #2563eb;
  color: white;
}

.nav-auth {
  border: 1px solid #e5e7eb;
}

.nav-right {
  display: flex;
  align-items: center;
}

.nav-button-logout {
  border-radius: 999px;
  border: 1px solid #f97373;
  background: #b91c1c;
  color: white;
  padding: 0.25rem 0.8rem;
  cursor: pointer;
  font-size: 0.85rem;
}

/* Contenido principal */
.main-content {
  padding: 1.5rem;
}
</style>
