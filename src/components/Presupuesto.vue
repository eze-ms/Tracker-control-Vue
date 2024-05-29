<script setup>
  import {  ref } from 'vue'
  import Alerta from './Alerta.vue'

  const presupuesto = ref(0)
  const error = ref('')

  // Definimos un emisor de eventos para emitir el evento 'definir-presupuesto'
  const emit = defineEmits(['definir-presupuesto'])

  const definirPresupuesto = () => {
    if(presupuesto.value <=0 || presupuesto.value =='') {
      error.value = 'Presupuesto no válido'

      setTimeout(() => {
        error.value = ''
      }, 3000);

      return
    }
    // Si la validación es exitosa, emitimos el evento 'definir-presupuesto' con el valor del presupuesto
    emit('definir-presupuesto', presupuesto.value)
  }
</script>

<template>
  <form 
    class="presupuesto"
    @submit.prevent="definirPresupuesto"
  >
    <Alerta
      v-if="error"
    >
    {{error}}

    </Alerta>

    <div class="campo">
      <label for="nuevo-presupuesto">Presupuesto</label>
      <input 
        id="nuevo-presupuesto"
        class="nuevo-presupuesto"
        placeholder="Añade tu presupuesto..."
        type="number"
        min="0"
        v-model.number="presupuesto"
      >
    </div>
    <input
      type="submit"
      value="Definir Presupuesto"
    >
  </form>
</template>

<style scoped>
  .presupuesto{
    width: 100%;
  }
  .campo{
    display: grid;
    gap: 2rem;
  }
  .presupuesto label{
    font-size: 2.2rem;
    text-align: center;
    color: var(--gris-oscuro);
  }
  .presupuesto input[type="number"] {
    background-color: #d5d5d8;
    border-radius: 1rem;
    color: var(--azul);
    padding: 1rem;
    border: 2.2rem;
    text-align: center;
  }
  .presupuesto input[type="submit"] {
    background-color: #ee6d47;
    padding: 1rem;
    border: none;
    text-align: center;
    font-size: 2rem;
    margin-top: 2rem;
    color: var(--blanco);
    font-weight: 600;
    width: 100%;
    letter-spacing: 0.5px;
    border-radius: 5px;
    transition: background-color 300ms ease;
  }
  .presupuesto input[type="submit"]:hover {
    background-color: #1048A4;
    cursor: pointer;
  }
</style>
