<script setup>
  import { ref, reactive, watch, computed, onMounted } from 'vue';
  import Presupuesto from './components/Presupuesto.vue';
  import ControlPresupuesto from './components/ControlPresupuesto.vue';
  import Modal from './components/Modal.vue';
  import Filtros from './components/Filtros.vue';
  import Gasto from './components/Gasto.vue';
  import { generarId } from './components/helpers';
  import iconoNuevoGasto from './assets/img/nuevo-gasto.svg';

  // Objeto reactivo para controlar la visibilidad y el estado de animación del modal
  const modal = reactive({
    mostrar: false,
    animar: false
  });

  // Referencias para el presupuesto, cantidad disponible y gastada
  const presupuesto = ref(0);
  const disponible = ref(0); // Cantidad disponible, cambia con el tiempo
  const gastado = ref(0);
  const filtro = ref('')

  // Objeto reactivo para un nuevo gasto
  const gasto = reactive({
    nombre: '',
    cantidad: '',
    categoria: '',
    id: null,
    fecha: Date.now()
  });

  // Referencia para la lista de gastos
  const gastos = ref([]);

  // Observador para actualizar el total gastado cuando la lista de gastos cambia
  watch(gastos, () => {
    const totalGastado = gastos.value.reduce((total, gasto) => gasto.cantidad + total, 0);
    gastado.value = totalGastado;
    disponible.value = presupuesto.value - totalGastado;

    // Almacena los gastos en LocalStorage
    localStorage.setItem('gastos', JSON.stringify(gastos.value));
  }, {
    deep: true
  });
  

  watch(modal, () => {
    if(!modal.mostrar){
      reiniciarStateGasto( );
    }
  }, {
    deep: true
  })

  watch(presupuesto, () => {
      localStorage.setItem('presupuesto', presupuesto.value);
    })

    onMounted(() => {
      const presupuestoStorage = localStorage.getItem('presupuesto')
      if(presupuestoStorage){
        presupuesto.value = Number(presupuestoStorage)
        disponible.value = Number(presupuestoStorage)
      }
      const gastosStorage = localStorage.getItem('gastos');
      if(gastosStorage){
        gastos.value = JSON.parse(gastosStorage)
      }
    })

  // Función para definir el presupuesto y la cantidad disponible
  const definirPresupuesto = (cantidad) => {
    presupuesto.value = cantidad;
    disponible.value = cantidad;
  };

  // Función para mostrar el modal y comenzar la animación
  const mostrarModal = () => {
    modal.mostrar = true;
    setTimeout(() => {
      modal.animar = true;
    }, 300);
  };

  // Función para ocultar el modal y detener la animación
  const ocultarModal = () => {
    modal.animar = false;
    setTimeout(() => {
      modal.mostrar = false;
    }, 300);
  };

  // Función para guardar un nuevo gasto, agregarlo a la lista y reiniciar el objeto gasto
  const guardarGasto = () => {
    if(gasto.id) {
      //editando
      const { id } = gasto
      const i = gastos.value.findIndex((gasto => gasto.id === id)) //Nos retorna la posición en el array
      gastos.value[i] = {...gasto} //Reescribe el gasto
    } else {
      //registro nuevo
      gastos.value.push({
      ...gasto,
      id: generarId()
    });
    }

    ocultarModal();
    reiniciarStateGasto();
  };

  const reiniciarStateGasto = () => {
    // Reiniciar el objeto gasto
    Object.assign(gasto, {
      nombre: '',
      cantidad: '',
      categoria: '',
      id: null,
      fecha: Date.now()
    })
  };

  const seleccionarGasto = id => {
    const gastoEditar = gastos.value.filter(gasto => gasto.id === id) [0]
    Object.assign(gasto, gastoEditar);
    mostrarModal()
  }

  const eliminarGasto = () => {
    if(confirm('Desea eliminar gasto?')){
      gastos.value = gastos.value.filter(gastoState => gastoState.id !== gasto.id)
      ocultarModal()
    }
  }

  const gastosFiltrados = computed(() => {
    if (filtro.value) {
      return gastos.value.filter(gasto => gasto.categoria === filtro.value);
    }
    return gastos.value; // Devuelve todos los gastos si no hay filtro
  })

  const resetApp = () => {
    if(confirm('¿Deseas reiniciar presupuesto y gastos?')){
      gastos.value = []
      presupuesto.value = 0 
    }
  }

</script>

<template>
  <div
    :class="{fijar: modal.mostrar}"
  >
    <header>
      <h1>Planificador de gastos</h1>

      <div class="contenedor-header contenedor sombra">
        <!-- Condicional para mostrar el componente Presupuesto o un mensaje de presupuesto válido -->
        <Presupuesto
          v-if="presupuesto === 0"
          @definir-presupuesto="definirPresupuesto" 
        />

        <ControlPresupuesto
          v-else
          :presupuesto="presupuesto"
          :disponible="disponible"
          :gastado="gastado"
          @reset-app="resetApp"
        />
      </div>
    </header>

    <main v-if="presupuesto > 0">
      <Filtros
        v-model:filtro="filtro"
      />
      <div class="listado-gastos contenedor">
        <h2>{{ gastosFiltrados.length > 0 ? 'Gastos' : 'No hay gastos' }}</h2>

        <Gasto
          v-for="gasto in gastosFiltrados" 
          :key="gasto.id"
          :gasto="gasto"
          @seleccionar-gasto="seleccionarGasto"
        />
      </div>

      <div class="crear-gasto">
        <img
          :src="iconoNuevoGasto"
          alt="icono-nuevo-gasto"
          @click="mostrarModal"
        /> 
      </div>
      <Modal
          v-if = "modal.mostrar"
          @ocultar-modal = "ocultarModal"
          @guardar-gasto="guardarGasto"
          @eliminar-gasto="eliminarGasto"
          :modal = "modal"
          :disponible="disponible"
          :id="gasto.id"
          v-model:nombre="gasto.nombre"
          v-model:cantidad="gasto.cantidad"
          v-model:categoria="gasto.categoria"
      />
    </main>
  </div>
</template>

<style>
  :root {
    --azul: #303642;
    --blanco: #eeeeee;
    --gris-claro: #3f4650;
    --gris: #303642;
    --gris-oscuro: #eeeeee;
    --negro: #000;
  }
  html {
    font-size: 62.5%;
    box-sizing: border-box;
  }
  *,
  *:before,
  *:after {
    box-sizing: inherit;
  }
  body {
    font-size: 1.6rem;
    font-family: "Lato", sans-serif;
    background-color: var(--azul);
  }
  h1 {
    font-size: 3rem;
    font-weight: 400;
    text-transform: uppercase;
    letter-spacing: 1px;
  }
  h2 {
    font-size: 3rem;
  }
  .fijar {
    overflow: hidden;
    height: 100vh;
  }
  header {
    background-color: var(--azul);
  }
  header h1 {
    padding: 3rem 0;
    margin: 0;
    color: var(--blanco);
    text-align: center;
  }
  .contenedor { 
      width: 90%;
      max-width: 80rem;
      margin: 0 auto;
  }
  .contenedor-header {
    margin-top: -5rem;
    transform: translateY(5rem);
    padding: 5rem;
  }
  .sombra {
    box-shadow: 0px 10px 15px -3px rgba(0,0,0,0.1);
    background-image: linear-gradient(45deg, #414550, #555962); 
    border-radius: 1.2rem;
    padding: 5rem;
  }
  
  .crear-gasto {
    position: fixed;
    bottom: 5rem;
    right: 5rem;
  }
  .crear-gasto img {
    width: 5rem;
    cursor:pointer;
  }
  .listado-gastos {
    margin-top: 10rem;
  }
  .listado-gastos h2 {
      font-weight: 900;
      color: var(--gris-oscuro);
  }

</style>
