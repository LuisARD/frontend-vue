
<script setup lang="ts">
import { ref, computed } from "vue";
import { useProductos } from "../features/useProductos";

const {
  productos,
  loading,
  errorMessage,
  successMessage,
  form,
  editMode,
  usuarioLogueado,
  cargarProductos,
  resetForm,
  seleccionarProducto,
  guardarProducto,
  borrarProducto,
} = useProductos();

// 🔹 Control del modal
const showModal = ref(false);

// Abrir modal para CREAR producto
function abrirModalCrear() {
  resetForm();
  showModal.value = true;
}

// Abrir modal para EDITAR producto
function abrirModalEditar(p: any) {
  seleccionarProducto(p);
  showModal.value = true;
}

// Cerrar modal
function cerrarModal() {
  showModal.value = false;
}

// Metodo del boton para guardar producto
async function onSubmit() {
  await guardarProducto();
  if (!errorMessage.value) {
    showModal.value = false;
  }
}

// Texto del título del modal
const tituloModal = computed(() =>
  editMode.value ? "Editar producto" : "Crear nuevo producto"
);
</script>

<template>
  <div class="productos-page">
    <header class="header">
      <h1>Productos</h1>
    </header>

    <!-- Sección de listado -->
    <section class="card">
      <div class="card-header">
        <h2>Lista de productos</h2>

        <div class="actions">
          <button class="refresh" @click="cargarProductos" :disabled="loading">
            🔄 Recargar
          </button>

          <!-- Botón AGREGAR solo si está logueado -->
          <button
            v-if="usuarioLogueado"
            class="add"
            type="button"
            @click="abrirModalCrear"
          >
            ➕ Agregar
          </button>
        </div>
      </div>

      <!-- 1) Si está cargando -->
      <p v-if="loading">Cargando...</p>

      <!-- 2) Si NO está cargando y HAY productos -->
<template v-else-if="productos.length">
  <table class="productos-table">
    <thead>
      <tr>
        <th>ID</th>
        <th>Nombre</th>
        <th>Descripción</th>
        <th>Precio</th>
        <th>Stock</th>
        <th v-if="usuarioLogueado">Acciones</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="p in productos" :key="p.id">
        <td>{{ p.id }}</td>
        <td>{{ p.nombre }}</td>
        <td>{{ p.descripcion }}</td>
        <td>{{ p.precio }}</td>
        <td>{{ p.stock }}</td>
        <td v-if="usuarioLogueado" class="acciones">
          <button @click="abrirModalEditar(p)">Editar</button>
          <button class="danger" @click="borrarProducto(p.id)">
            Eliminar
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</template>


      <!-- 3) Si NO está cargando y NO hay productos -->
      <p v-else>No hay productos registrados.</p>
    </section>

    <!-- Mensajes globales -->
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    <p v-if="successMessage" class="success">{{ successMessage }}</p>

    <!-- MODAL de crear/editar producto -->
    <div v-if="showModal" class="modal-backdrop">
      <div class="modal">
        <header class="modal-header">
          <h2>{{ tituloModal }}</h2>
          <button class="close-btn" @click="cerrarModal">✖</button>
        </header>

        <p v-if="!usuarioLogueado" class="info">
          Debes iniciar sesión para gestionar productos.
        </p>

        <form v-if="usuarioLogueado" @submit.prevent="onSubmit">
          <div class="form-row">
            <label>Nombre</label>
            <input v-model="form.nombre" type="text" required />
          </div>
          <div class="form-row">
            <label>Descripción</label>
            <input v-model="form.descripcion" type="text" required />
          </div>
          <div class="form-row">
            <label>Precio</label>
            <input
              v-model.number="form.precio"
              type="number"
              min="0"
              step="0.01"
              required
            />
          </div>
          <div class="form-row">
            <label>Stock</label>
            <input
              v-model.number="form.stock"
              type="number"
              min="0"
              step="1"
              required
            />
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
.productos-page {
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
  background: #16a34a;
  color: white;
}

/* Tabla */
.productos-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.9rem;
}

.productos-table th,
.productos-table td {
  border-bottom: 1px solid #e5e7eb;
  padding: 0.4rem 0.5rem;
  text-align: left;
}

.productos-table th {
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

.error {
  color: #b91c1c;
  margin-top: 0.5rem;
}

.success {
  color: #15803d;
  margin-top: 0.5rem;
}

.info {
  font-size: 0.9rem;
  color: #4b5563;
}

/* 🔹 Estilos del modal */
.modal-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(15, 23, 42, 0.35);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 40;
}

.modal {
  background: white;
  border-radius: 12px;
  padding: 1.25rem 1.5rem;
  width: 100%;
  max-width: 420px;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.25);
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
  justify-content: flex-end;
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
</style>
