
<script setup>
    defineProps(['filtro'])
    defineEmits(['update:filtro'])
</script>
<template>
    <div class=" filtros sombra contenedor">
        <form>
            <div class="campo">
                <label for="filtro">Elegir Gastos</label>
                <select
                    id="filtro"
                    :value="filtro"
                    @input="$emit('update:filtro', $event.target.value)"
                    
                >
                    <option value="">-- Seleccione --</option>
                    <option value="ahorro">Ahorro</option>
                    <option value="comida">Comida</option>
                    <option value="casa">Casa</option>
                    <option value="gastos">Gastos Varios</option>
                    <option value="ocio">Ocio</option>
                    <option value="salud">Salud</option>
                    <option value="suscripciones">Suscripciones</option>
                </select>
            </div>
        </form>
    </div>
</template>

<style scoped>
    .filtros{
        margin-top: 10rem;
    }
    .filtros .campo{
        display: flex;
        align-items: center;
        gap: 2rem
    }
    .filtros label{
        font-size: 2rem;
        font-weight: 400;
        color: #ededed;
    }
    .filtros select{
        flex: 1;
        padding: 1rem;
        border: none;
        border-radius: 1rem;
        background-color: #d5d5d8;
        text-align: center;
    }
</style>