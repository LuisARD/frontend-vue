<script setup lang="ts">
import { useVentas } from "../features/useVentas";

const {
  ventas,
  clientes,
  productos,
  ventaForm,
  loading,
  errorMessage,
  successMessage,
  totalCalculado,
  agregarLinea,
  eliminarLinea,
  guardarVenta,
  borrarVenta,
  getProductoById,
  getClienteNombre,  
  getProductoNombre,
} = useVentas();
</script>

<template>
  <div class="ventas-page">
    <header class="header">
      <h1>Ventas</h1>
      <p class="subtitle">
        Aquí puedes registrar nuevas ventas y ver el historial.
      </p>
    </header>

    <!-- Listado de ventas -->
<template v-if="ventas.length">
  <table class="ventas-table">
  <thead>
    <tr>
      <th>ID</th>
      <th>Fecha</th>
      <th>Cliente</th>
      <th>Detalles</th>
      <th>Total</th>
      <th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="v in ventas" :key="v.id">
      <td>{{ v.id }}</td>
      <td>{{ new Date(v.fecha).toLocaleString() }}</td>

      <!-- Nombre del cliente -->
      <td>{{ getClienteNombre(v.clienteId) }}</td>

      <!-- Detalles (producto + cantidad) -->
      <td>
        <ul class="detalle-lista">
          <li
            v-for="det in v.detalles"
            :key="det.productoId + '-' + det.cantidad"
          >
            {{ getProductoNombre(det.productoId) }} — Cant: {{ det.cantidad }}
          </li>
        </ul>
      </td>

      <td>{{ v.total }}</td>

      <td class="acciones">
        <button class="danger" @click="borrarVenta(v.id)">
          Eliminar
        </button>
      </td>
    </tr>
  </tbody>
</table>
</template>

<p v-else>No hay ventas registradas.</p>


    <!-- Formulario de nueva venta -->
    <section class="card form-card">
      <h2>Registrar nueva venta</h2>

      <form @submit.prevent="guardarVenta">
        <div class="form-row">
          <label>Fecha</label>
          <input
            v-model="ventaForm.fecha"
            type="datetime-local"
            required
          />
        </div>

        <div class="form-row">
          <label>Cliente</label>
          <select v-model.number="ventaForm.clienteId" required>
            <option :value="null" disabled>Selecciona un cliente</option>
            <option v-for="c in clientes" :key="c.id" :value="c.id">
              {{ c.nombre }}
            </option>
          </select>
        </div>

        <div class="detalle-section">
          <div class="detalle-header">
            <h3>Detalle de venta</h3>
            <button type="button" class="add-line" @click="agregarLinea">
              ➕ Agregar línea
            </button>
          </div>

          <div
            class="detalle-row"
            v-for="(linea, index) in ventaForm.detalles"
            :key="index"
          >
            <div class="detalle-field">
              <label>Producto</label>
              <select v-model.number="linea.productoId" required>
                <option :value="null" disabled>Selecciona un producto</option>
                <option v-for="p in productos" :key="p.id" :value="p.id">
                  {{ p.nombre }} ({{ p.precio }})
                </option>
              </select>
            </div>

            <div class="detalle-field">
              <label>Cantidad</label>
              <input
                v-model.number="linea.cantidad"
                type="number"
                min="1"
                required
              />
            </div>

            <div class="detalle-field subtotal">
              <label>Subtotal</label>
              <span>
                {{
                  getProductoById(linea.productoId)
                    ? (getProductoById(linea.productoId)!.precio *
                        (linea.cantidad || 0)
                      ).toFixed(2)
                    : "0.00"
                }}
              </span>
            </div>

            <button
              type="button"
              class="remove-line"
              @click="eliminarLinea(index)"
              v-if="ventaForm.detalles.length > 1"
            >
              ✖
            </button>
          </div>
        </div>

        <div class="total-row">
          <span>Total:</span>
          <strong>{{ totalCalculado.toFixed(2) }}</strong>
        </div>

        <div class="form-actions">
          <button type="submit" :disabled="loading">
            {{ loading ? "Guardando..." : "Registrar venta" }}
          </button>
        </div>
      </form>

      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
      <p v-if="successMessage" class="success">{{ successMessage }}</p>
    </section>
  </div>
</template>


<style scoped>
.ventas-page {
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
  margin-bottom: 0.75rem;
}

/* Tabla de ventas */
.ventas-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.9rem;
}

.ventas-table th,
.ventas-table td {
  border-bottom: 1px solid #e5e7eb;
  padding: 0.4rem 0.5rem;
  text-align: left;
}

.ventas-table th {
  background: #f3f4f6;
}

.acciones .danger {
  border-radius: 999px;
  border: none;
  padding: 0.25rem 0.5rem;
  cursor: pointer;
  font-size: 0.8rem;
  background: #fee2e2;
  color: #b91c1c;
}

/* Formulario */
.form-card {
  margin-top: 0.5rem;
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

.form-row input,
.form-row select {
  padding: 0.4rem 0.5rem;
  border-radius: 6px;
  border: 1px solid #d1d5db;
  font-size: 0.9rem;
}

/* Detalle */
.detalle-section {
  margin-top: 0.75rem;
  border-top: 1px solid #e5e7eb;
  padding-top: 0.75rem;
}

.detalle-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.5rem;
}

.add-line {
  border: none;
  border-radius: 999px;
  padding: 0.25rem 0.6rem;
  cursor: pointer;
  font-size: 0.8rem;
  background: #e0f2fe;
  color: #0369a1;
}

.detalle-row {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr auto;
  gap: 0.5rem;
  align-items: end;
  margin-bottom: 0.5rem;
}

.detalle-field {
  display: flex;
  flex-direction: column;
}

.detalle-field label {
  font-size: 0.8rem;
  margin-bottom: 0.15rem;
}

.detalle-field select,
.detalle-field input {
  padding: 0.35rem 0.5rem;
  border-radius: 6px;
  border: 1px solid #d1d5db;
  font-size: 0.85rem;
}

.detalle-field.subtotal span {
  padding: 0.35rem 0.5rem;
  border-radius: 6px;
  background: #f9fafb;
  border: 1px solid #e5e7eb;
}

.remove-line {
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 1rem;
  color: #b91c1c;
}

/* Total */
.total-row {
  display: flex;
  justify-content: flex-end;
  gap: 0.5rem;
  margin-top: 0.75rem;
  font-size: 1rem;
}

/* Botones */
.form-actions {
  margin-top: 0.75rem;
}

.form-actions button {
  border-radius: 999px;
  border: none;
  padding: 0.45rem 0.9rem;
  cursor: pointer;
  font-size: 0.9rem;
  background: #16a34a;
  color: white;
}

.error {
  color: #b91c1c;
  margin-top: 0.5rem;
}

.success {
  color: #15803d;
  margin-top: 0.5rem;
}
</style>
