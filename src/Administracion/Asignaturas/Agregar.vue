<template>
    <div class="container z-depth-1 mt-4">
        <Preloader v-if="isLoading" />
        <div class="row">
            <nav>
                <div class="nav-wrapper blue-colegio">
                    <div class="col s12">
                        <router-link to="/administracion" class="breadcrumb">Administración</router-link>
                        <router-link to="/administracion/asignaturas" class="breadcrumb">Asignaturas y Seminarios</router-link>
                        <router-link to="/administracion/nueva-asignatura" class="breadcrumb">Agregar</router-link>
                    </div>
                </div>
            </nav>
        </div>
        <div class="row">
            <form class="col s12 m12">
                <div class="row">
                    <div class="col s12 m6 input-field">
                        <input type="text" v-model="asignatura.nombre" id="nombre"/>
                        <label for="nombre">Nombre</label>
                    </div>
                    <div class="col s12 m3 input-field">
                        <select title="asd" id="tipo" ref="tipo" v-model="asignatura.tipo">
                            <option v-for="tipo in catalogos.tipos" v-bind:key="tipo">
                                {{ tipo }}
                            </option>
                        </select>
                        <label for="tipo">Tipo</label>
                    </div>
                    <div class="col s12 m3 input-field">
                        <select title="asd" id="nivel" ref="nivel" v-model="asignatura.nivel">
                            <option value=""></option>
                            <option v-for="nivel in catalogos.niveles" v-bind:key="nivel">
                                {{ nivel }}
                            </option>
                        </select>
                        <label for="nivel">Nivel Escolar</label>
                    </div>
                </div>
                <div class="row">
                    <div class="col s12 m2 input-field">
                        <select title="asd" id="semestre" ref="semestre" v-model="asignatura.semestre">
                            <option value=""></option>
                            <option v-for="semestre in catalogos.semestres" v-bind:key="semestre._id" v-bind:value="semestre">
                                {{ semestre.nombre }}
                            </option>
                        </select>
                        <label for="semestre">Semestre</label>
                    </div>
                    <div class="col s12 m5 input-field">
                        <select title="asd" id="docente" ref="docente" v-model="asignatura.docente_asignado">
                            <option value=""></option>
                            <option v-for="semestre in catalogos.semestres" v-bind:key="semestre">
                                {{ semestre }}
                            </option>
                        </select>
                        <label for="docente">Docente Asignado</label>
                    </div>
                    <div class="col s12 m5 input-field">
                        <textarea class="materialize-textarea" v-model="asignatura.observaciones" id="observaciones"></textarea>
                        <label for="observaciones">Observaciones</label>
                    </div>
                </div>
                <button 
                    class="blue-colegio waves-effect btn right" 
                    style="margin-bottom:2em;" 
                    :disabled="(asignatura.nombre == '' || asignatura.nivel == '' || asignatura.tipo == '' || asignatura.semestre == '')"
                    @click.prevent="guardarAsignatura()"
                    >
                    Guardar
                </button>
            </form>
        </div>
    </div>
</template>

<script>
import {api_url, store} from "../../main"
import router from "../../router/index"
import M from "materialize-css"
import Preloader from "../../components/Preloader.vue"

export default({
    components:{
        Preloader
    },
    mounted(){
        this.logged_in = store.state.user.token != "" && localStorage.getItem('token') != null
        this.check_login()
        for (let index = 0; index < 8; index++) {
            this.catalogos.semestres.push('SEMESTRE ' + (index + 1))
        }
        this.obtenerCatalogos()
    },
    methods: {
        check_login(){
            if(!this.logged_in){
                router.push('/administracion/login')
            }
            this.uname = store.state.user.name
        },
        async obtenerCatalogos(){
            this.isLoading = true
            await fetch(api_url + '/administracion/asignaturas/catalogos', {
                method: 'GET',
                headers:{
                    'Content-Type': 'application/json',
                    'Authorization': 'Bearer ' + store.state.user.token
                }
            }).then((request) => {
                request.json().then((response) => {
                    if(response.status != 'success'){
                        if(response.msg != "" && response.msg != undefined){
                            store.commit('logout')
                        } else {
                            M.toast({ html: 'Error al obtener la información del servidor', classes: 'red darken-2' })
                            return false
                        }
                    }
                    this.catalogos.programas = response.catalogos.programas
                    this.catalogos.semestres = response.catalogos.periodos
                }).finally(() => {
                    var selects = document.querySelectorAll('select')
                    M.FormSelect.init(selects, {})
                })
            }).finally(() => { this.isLoading= false })
        },
        async guardarAsignatura(){
            this.isLoading = true
            await fetch(api_url + '/administracion/asignaturas/agregar', {
                method: 'POST',
                headers:{
                    'Content-Type': 'application/json',
                    'Authorization': 'Bearer ' + store.state.user.token
                },
                body: JSON.stringify(this.asignatura)
            }).then((request) => {
                request.json().then((response) => {
                    if(response.status != 'success'){
                        if(response.msg != "" && response.msg != undefined){
                            store.commit('logout')
                        } else {
                            M.toast({ html: 'Error al obtener la información del servidor', classes: 'red darken-2' })
                            return false
                        }
                    }
                    M.toast({ html: response.message, classes: 'green darken-2'})
                })
            }).finally(() => { 
                this.isLoading = false 
                this.asignatura = {
                    nombre: '',
                    nivel: '',
                    docente_asignado: '',
                    docente_propuesto: '',
                    semestre: ''
                }
                var selects = document.querySelectorAll('select')
                selects.selectedIndex = 0
                M.FormSelect.init(selects, {})
            })
        }
    },
    data() {
        return {
            asignatura: {
                nombre: '',
                nivel: '',
                docente_asignado: '',
                observaciones: '',
                programa: '',
                semestre: ''
            },
            catalogos:{
                niveles: [
                    'LICENCIATURA', 'MAESTRÍA', 'DOCTORADO'
                ],
                tipos: [
                    'Asignatura', 'Seminario'
                ],
                programas: [],
                semestres: []
            },
            isLoading: false
        }
    }
})
</script>
