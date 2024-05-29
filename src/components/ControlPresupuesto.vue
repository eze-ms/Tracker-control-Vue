<script setup>
    import { computed } from 'vue';
    import CircleProgress from 'vue3-circle-progress';
    import "vue3-circle-progress/dist/circle-progress.css"
    import { formatearCantidad } from './helpers';

    defineEmits(['reset-app'])

    const props = defineProps({
        presupuesto: {
            type: Number,
            require: true
        },
        disponible: {
            type: Number,
            require: true
        },
        gastado: {
            type: Number,
            require: true
        }
    })

    const porcentaje = computed (() =>{
        return parseInt(((props.presupuesto - props.disponible) / props.presupuesto * 100))
    })

</script>

<template>
    <div class="dos-columnas">
        <div class="contenedor-grafico">
            <p class="porcentaje">{{porcentaje}}%</p>
            <CircleProgress
                :percent="porcentaje"
                :size="250"
                :border-width="30"
                :border-bg-width="30"
                fill-color="#ee6d47"
                empty-color="#d5d5d8"
            />
        </div>
        <div class="contenedor-presupuesto">
           
            <p>
                <span>Presupuesto:</span>
                {{ formatearCantidad(presupuesto) }}
            </p>
            <p>
                <span>Disponible:</span>
                {{ formatearCantidad (disponible) }}
            </p>
            <p>
                <span>Gastos:</span>
                {{ formatearCantidad (gastado) }}
            </p>
            <button
                class="reset-app"
                type="button"
                @click="$emit('reset-app')"
            >Reset
            </button>
        </div>
    </div>
</template>

<style scoped>
    .contenedor-grafico{
        position: relative;
    }
    .porcentaje{
        position: absolute;
        margin: auto;
        top: calc(50% - 1.5rem);
        left: 0;
        right: 0;
        text-align: center;
        z-index: 100;
        font-size: 4rem;
        font-weight: 900;
        color: #fff;
    }
    .dos-columnas {
        display: flex;
        flex-direction: column;
    }
    .dos-columnas > :first-child{
        margin-bottom: 3rem;
    }
    
    @media (min-width: 768px) {
        .dos-columnas {
            flex-direction: row;
            gap: 4rem;
        }
        .dos-columnas > :first-child{
            margin-bottom: 0;
        }
    }
  
    @media (max-width: 768px) {
        .contenedor-presupuesto {
            display: flex;
            flex-direction: column; 
            align-items: center; 
        }
        .contenedor-presupuesto p{
            margin: 1rem;
        }
        
    }

    .reset-app{
        margin-top: 4rem;
        background-color:#ee6d47;
        border: none;
        padding: 1rem;
        width: 50%;
        color: #fff;
        text-transform: uppercase;
        border-radius: 2rem;
        letter-spacing: 0.04rem;
        transition-property: background-color;
        transition-duration: 300ms;
    }
    .reset-app:hover{
        cursor: pointer;
        background-color:#923a20;
    }
    .contenedor-presupuesto{
        width: 100%;
    }
    .contenedor-presupuesto p{
        font-size: 2.2rem;
        letter-spacing: 0.04rem;
        text-align: center;
        color: var(--gris-oscuro);
    }
    @media (min-width: 768px){
        .contenedor-presupuesto p{
            text-align: left;
        }
    }
    .contenedor-presupuesto span{
        font-weight: 600;
        color: #ededed;
    }
</style>
