<script setup>
import { ref, computed } from 'vue'
import Alerta from './TheAlerta.vue'
import cerrarModal from '../assets/img/cerrar.svg'

const error = ref('')

const emit = defineEmits([
  'ocultar-modal',
  'update:nombre',
  'update:cantidad',
  'update:categoria',
  'guardar-gasto'
])
const props = defineProps({
  modal: {
    type: Object,
    required: true
  },
  nombre: {
    type: String,
    required: true
  },
  cantidad: {
    type: [Number, String],
    required: true
  },
  categoria: {
    type: String,
    required: true
  },
  disponible: {
    type: Number,
    required: true
  },
  id: {
    type: [String, null],
    required: true
  }
})

const old = props.cantidad

const agregarGasto = () => {
  /* TODO: Validar que no haya campos vacíos */
  const { nombre, cantidad, categoria, disponible, id } = props
  if ([nombre, cantidad, categoria].includes('')) {
    error.value = 'Todos los campos son obligatorios'
    console.log('hay campos vacíos')
    setTimeout(() => {
      error.value = ''
    }, 3000)
    return
  }
  /* TODO: validar la cantidad */
  if (cantidad <= 0) {
    error.value = 'Cantidad no válida'
    setTimeout(() => {
      error.value = ''
    }, 3000)
    return
  }

  /* TODO: validar que el usuario no gaste mas de su presupuesto */
  if (id) {
    if (cantidad > disponible + old) {
      error.value = 'Ha sobrepasado el presupuesto disponible'
      setTimeout(() => {
        error.value = ''
      }, 3000)
      return
    }
  } else {
    if (cantidad > disponible) {
      error.value = 'Ha sobrepasado el presupuesto disponible'
      setTimeout(() => {
        error.value = ''
      }, 3000)
      return
    }
  }

  emit('guardar-gasto')
}

const isEditing = computed(() => {
  return props.id
})
</script>

<template>
  <div class="modal">
    <div class="cerrar-modal">
      <img :src="cerrarModal" alt="cerrar modal" @click="$emit('ocultar-modal')" />
    </div>
    <div class="contenedor contenedor-formulario" :class="[modal.animar ? 'animar' : 'cerrar']">
      <form class="nuevo-gasto" @submit.prevent="agregarGasto">
        <Alerta v-if="error" :mensaje="error" :tipo="error">{{ error }}</Alerta>
        <legend>{{ isEditing ? 'Editar gasto' : 'Nuevo gasto' }}</legend>

        <div class="campo">
          <label for="nombre">Nombre gasto:</label>
          <input
            type="text"
            id="nombre"
            placeholder="ej: Almuerzo"
            :value="nombre"
            @input="$emit('update:nombre', $event.target.value)"
          />
        </div>
        <div class="campo">
          <label for="cantidad">Cantidad:</label>
          <input
            type="number"
            id="cantidad"
            placeholder="ej: 30.000"
            :value="cantidad"
            @input="$emit('update:cantidad', +$event.target.value)"
          />
        </div>
        <div class="campo">
          <label for="categoria">Categoría:</label>
          <select
            id="categoria"
            :value="categoria"
            @input="$emit('update:categoria', $event.target.value)"
          >
            <option value="">-- Seleccione --</option>
            <option value="ahorro">Ahorro</option>
            <option value="comida">Comida</option>
            <option value="casa">Casa</option>
            <option value="cuentas">Cuentas</option>
            <option value="ocio">Ocio</option>
            <option value="salud">Salud</option>
            <option value="suscripciones">Suscripciones</option>
          </select>
        </div>
        <input type="submit" :value="[isEditing ? 'Guardar Cambios' : 'Añadir gasto']" />
      </form>
    </div>
  </div>
</template>

<style scoped>
.modal {
  position: absolute;
  background-color: rgb(0 0 0 / 0.9);
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}
.cerrar-modal {
  position: absolute;
  right: 3rem;
  top: 3rem;
  cursor: pointer;
}
.cerrar-modal img {
  width: 3rem;
}

.contenedor-formulario {
  transition-property: all;
  transition-duration: 300ms;
  transition-timing-function: ease-in;
  opacity: 0;
}
.contenedor-formulario.animar {
  opacity: 1;
}
.contenedor-formulario.cerrar {
  opacity: 0;
}
.nuevo-gasto {
  margin: 10rem auto 0 auto;
  display: grid;
  gap: 2rem;
}
.nuevo-gasto legend {
  text-align: center;
  color: var(--blanco);
  font-size: 3rem;
  font-weight: 900;
}
.campo {
  display: grid;
  gap: 2rem;
}
.nuevo-gasto input,
.nuevo-gasto select {
  background-color: var(--gris-claro);
  border-radius: 1rem;
  padding: 1rem;
  border: none;
  font-size: 2.2rem;
}
.nuevo-gasto label {
  color: var(--blanco);
  font-size: 3rem;
}
.nuevo-gasto input[type='submit'] {
  background-color: var(--azul);
  color: var(--blanco);
  font-size: 2.2rem;
  font-weight: 900;
  cursor: pointer;
}
</style>
