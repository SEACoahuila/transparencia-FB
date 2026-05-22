<script setup>
import { ref, watch } from 'vue';
import { useModuloStore } from '../../stores/modulo';

const props = defineProps({
    currentUser:{
        type:Object,
        required:true
    },
    departamentos:{
        type:Array,
        required:true
    },
    articulos:{
        type:Array,
        required:true
    }
});
const modulo = useModuloStore();

const titulo = ref('');
const articulo = ref('');
const fraccion = ref('');
const descripcion = ref('');
const encargado = ref('');
const nota = ref('');

watch(
    () => props.articulos,
    (value) => {
        if (!articulo.value && value && value.length > 0) {
            articulo.value = value[0].id;
        }
    },
    { immediate: true }
);

watch(
    () => props.departamentos,
    (value) => {
        if (!encargado.value && value && value.length > 0) {
            encargado.value = value[0].id;
        }
    },
    { immediate: true }
);

const crearModulo = async() => {
    const datos = {
        titulo: titulo.value.trim(),
        descripcion: descripcion.value.trim() ? [{ orden: '20230001', valor: descripcion.value.trim() }] : [],
        encargado: encargado.value,
        nota: nota.value.trim(),
        articulo: articulo.value,
        fraccion: fraccion.value.toString().trim(),
    };

    await modulo.nuevoModulo(datos);
}
</script>

<template>
    <div class="modal fade" id="ModalNuevoModulo" aria-hidden="true" aria-labelledby="ModalNuevoModuloLabel" tabindex="-1">
        <div class="modal-dialog modal-dialog-centered modal-lg">
            
            <div class="modal-content">

                <div class="modal-header">
                    <h1 class="modal-title fs-5" id="nuevaSeccionModalLabel">
                        Crear nuevo módulo
                    </h1>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <form name="nuevoModulo" @submit.prevent="crearModulo()">
                    <div class="modal-body">
                        
                        <div class="row p-3">
                            <Loading v-if="modulo.loading"></Loading>
                            <Error v-if="modulo.message.error">{{ modulo.message.text }}</Error>
                            <Success v-if="modulo.message.success">{{ modulo.message.text }}</Success>

                            <div class="col-12 mb-3">
                                <label for="titulo" class="form-label">Título</label>
                                <input v-model="titulo" required type="text" class="form-control" placeholder="Escribe el título del módulo" id="titulo" name="titulo">
                            </div>

                            <div class="col-md-6 mb-3">
                                <label for="articulo" class="form-label">Artículo</label>
                                <select v-model="articulo" required class="form-control" id="articulo" name="articulo">
                                    <option v-for="a in articulos" :key="a.id" :value="a.id">{{ a.titulo }}</option>
                                </select>
                                
                            </div>

                            <div class="col-md-6 mb-3">
                                <label for="fraccion" class="form-label">Fracción</label>
                                <input v-model="fraccion" required type="number" class="form-control" placeholder="Escribe el número de fracción" id="fraccion" name="fraccion">
                            </div>
                            
                            <div class="col-12 mb-3">
                                <label for="descripcion" class="form-label">Descripción (opcional)</label>
                                <textarea v-model="descripcion" class="form-control" id="descripcion" name="descripcion" placeholder="Escribe una descripción"></textarea>
                            </div>
                            
                            <hr>
                            <label class="mb-3">ENCARGADO</label>
                            <div class="col-md-12 mb-3">
                                <label for="encargado" class="form-label">El titular del departamento:</label>
                                <select v-model="encargado" class="form-control" id="encargado" name="encargado">
                                    <option v-for="dpto in departamentos" :key="dpto.id" :value="dpto.id">{{dpto.nombre}}</option>
                                </select>
                            </div>

                            
                            <hr class="mt-3">
                            
                            <div class="col-12 mb-3">
                                <label for="nota" class="form-label">Nota de cierre (opcional)</label>
                                <textarea v-model="nota" class="form-control" id="nota" name="nota" placeholder="Escribe una nota de cierre"></textarea>
                            </div>
                        </div>
    
                    </div>
                    
                    <div class="modal-footer">
                        <button class="btn btn-secondary" type="submit">Crear</button>
                    </div>
                </form>


            </div>

        </div>
    </div>
</template>