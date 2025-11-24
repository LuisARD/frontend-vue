
<script setup lang="ts">
import { ref, computed } from "vue";
import { useClientes } from "../features/UseClientes";

const {
  clientes,
  loading,
  errorMessage,
  successMessage,
  form,
  editMode,
  cargarClientes,
  resetForm,
  seleccionarCliente,
  guardarCliente,
  borrarCliente,
} = useClientes();

// 🔹 Control del modal
const showModal = ref(false);

// Abrir modal para CREAR cliente
function abrirModalCrear() {
  resetForm();
  showModal.value = true;
}

// Abrir modal para EDITAR cliente
function abrirModalEditar(c: any) {
  seleccionarCliente(c);
  showModal.value = true;
}

// Cerrar modal
function cerrarModal() {
  showModal.value = false;
}

// Metodo del boton del click para el boton de guardar
async function onSubmit() {
  await guardarCliente();
  if (!errorMessage.value) {
    showModal.value = false;
  }
}

// Texto del título del modal
const tituloModal = computed(() =>
  editMode.value ? "Editar cliente" : "Crear nuevo cliente"
);
</script>

<template>
  <div class="clientes-page">
    <header class="header">
      <h1>Clientes</h1>
    </header>

    <!-- Lista de clientes -->
    <section class="card">
      <div class="card-header">
        <h2>Lista de clientes</h2>

        <div class="actions">
          <button class="refresh" @click="cargarClientes" :disabled="loading">
            🔄 Recargar
          </button>

          <button class="add" type="button" @click="abrirModalCrear">
            ➕ Agregar
          </button>
        </div>
      </div>

      <!-- 1) Si está cargando -->
      <p v-if="loading">Cargando...</p>

      <!-- 2) Si NO está cargando y HAY clientes -->
      <template v-else-if="clientes.length">
        <table class="clientes-table">
          <thead>
            <tr>
              <th>ID</th>
              <th>Nombre</th>
              <th>Email</th>
              <th>Teléfono</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="c in clientes" :key="c.id">
              <td>{{ c.id }}</td>
              <td>{{ c.nombre }}</td>
              <td>{{ c.email }}</td>
              <td>{{ c.telefono }}</td>
              <td class="acciones">
                <button @click="abrirModalEditar(c)">Editar</button>
                <button class="danger" @click="borrarCliente(c.id)">
                  Eliminar
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </template>

      <!-- 3) Si NO está cargando y NO hay clientes -->
      <p v-else>No hay clientes registrados.</p>
    </section>

    <!-- Mensajes globales -->
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    <p v-if="successMessage" class="success">{{ successMessage }}</p>

    <!-- MODAL de crear/editar cliente -->
    <div v-if="showModal" class="modal-backdrop">
      <div class="modal">
        <header class="modal-header">
          <h2>{{ tituloModal }}</h2>
          <button class="close-btn" @click="cerrarModal">✖</button>
        </header>

        <form @submit.prevent="onSubmit">
          <div class="form-row">
            <label>Nombre</label>
            <input v-model="form.nombre" type="text" required />
          </div>
          <div class="form-row">
            <label>Email</label>
            <input v-model="form.email" type="email" required />
          </div>
          <div class="form-row">
            <label>Teléfono</label>
            <input v-model="form.telefono" type="text" required />
          </div>

          <div class="form-actions">
            <button type="submit" :disabled="loading">
              {{ loading ? "Guardando..." : editMode ? "Actualizar" : "Crear" }}
            </button>
            <button type="button" class="secondary" @click="cerrarModal">
              Cancelar
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
.clientes-page {
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
}

.header {
  margin-bottom: 0.5rem;
}

.subtitle {
  font-size: 0.9rem;
  color: #4b5563;
}

.card {
  background: white;
  border-radius: 10px;
  padding: 1rem 1.25rem;
  box-shadow: 0 2px 8px rgba(15, 23, 42, 0.05);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.75rem;
}

.actions {
  display: flex;
  gap: 0.5rem;
}

.refresh,
.add {
  border: none;
  border-radius: 999px;
  padding: 0.3rem 0.7rem;
  cursor: pointer;
  font-size: 0.85rem;
}

.refresh {
  background: #e5e7eb;
}

.add {
  background: #2563eb;
  color: white;
}

/* Tabla */
.clientes-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.9rem;
}

.clientes-table th,
.clientes-table td {
  border-bottom: 1px solid #e5e7eb;
  padding: 0.4rem 0.5rem;
  text-align: left;
}

.clientes-table th {
  background: #f3f4f6;
}

.acciones button {
  margin-right: 0.25rem;
  border-radius: 999px;
  border: none;
  padding: 0.25rem 0.5rem;
  cursor: pointer;
  font-size: 0.8rem;
}

.acciones .danger {
  background: #fee2e2;
  color: #b91c1c;
}

/* Modal */
.modal-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(15, 23, 42, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 40;
}

.modal {
  background: white;
  border-radius: 10px;
  padding: 1.25rem 1.5rem;
  width: 100%;
  max-width: 420px;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.3);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.75rem;
}

.close-btn {
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 1rem;
}

.form-row {
  display: flex;
  flex-direction: column;
  margin-bottom: 0.75rem;
}

.form-row label {
  font-size: 0.85rem;
  margin-bottom: 0.2rem;
}

.form-row input {
  padding: 0.4rem 0.5rem;
  border-radius: 6px;
  border: 1px solid #d1d5db;
  font-size: 0.9rem;
}

.form-actions {
  display: flex;
  gap: 0.5rem;
  margin-top: 0.5rem;
}

.form-actions button {
  border-radius: 999px;
  border: none;
  padding: 0.4rem 0.8rem;
  cursor: pointer;
  font-size: 0.9rem;
}

.form-actions button[type="submit"] {
  background: #16a34a;
  color: white;
}

.form-actions .secondary {
  background: #e5e7eb;
  color: #111827;
}

/* Mensajes */
.error {
  color: #b91c1c;
  margin-top: 0.5rem;
}

.success {
  color: #15803d;
  margin-top: 0.5rem;
}
</style>
