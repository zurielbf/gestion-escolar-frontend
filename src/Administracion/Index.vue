<template>
    <div class="container z-depth-1 mt-3">
        <Preloader v-if="isLoading" />
        <div class="row">
            <div v-if="logged_in && request_finished" class="white-text center" style="padding:10px;" :class="{ ' teal darken-1': (periodo_actual.nombre != '' && periodo_actual.activo ), 'red darken-2': (periodo_actual.nombre == '' || !periodo_actual.activo) }">
                <span style="margin-left:2em; margin-right:2em;" v-if="periodo_actual.nombre != '' && periodo_actual.activo">
                    El periodo actual es: {{ periodo_actual.nombre }}, "{{ periodo_actual.descripcion }}"
                </span>
                <span style="margin-left:2em; margin-right:2em;" v-else-if="!periodo_actual.activo && periodo_actual.nombre != ''">
                    El periodo {{ periodo_actual.nombre }} No se encuentra activo
                    <a :href="'/administracion/periodo/' + periodo_actual._id" style="margin-left:2em; margin-top:-0.4em;" class="btn-small waves-effect btn-flat white">Editar Periodo</a>
                </span>
                <span style="margin-left:2em; margin-right:2em;" v-else>
                    No hay periodos configurados <a href="/administracion/agregar-periodo" style="margin-left:2em; margin-top:-0.4em;" class="btn-small waves-effect btn-flat white">Agregar uno</a>
                </span>
            </div>
        </div>
        <div class="row">
            <div class="col l4 m2 s12" v-if="$store.state.user.permisos.includes('alumnos.list') || $store.state.user.permisos.includes('alumnos.add')">
                <div class="row">
                    <div class="col s12 m12">
                        <div class="card blue-colegio darken-1 sticky-action unselectable">
                            <div class="card-content white-text text-center">
                                <span class="material-icons" style="font-size: 48px">school</span>
                                <span class="card-title">
                                    {{ numero_alumnos }} Alumnos
                                </span>
                            </div>
                            <div class="card-action">
                                <router-link v-if="$store.state.user.permisos.includes('alumnos.list')" style="color:white;" to="/administracion/alumnos" class="waves-effect btn-flat waves-light"><i class="material-icons left">format_list_bulleted</i>Listado</router-link>
                                &nbsp;
                                <router-link v-if="$store.state.user.permisos.includes('alumnos.add')" style="color:white;" to="/administracion/nuevo-alumno" class="waves-effect btn-flat waves-light"><i class="material-icons left">person_add</i>Nuevo</router-link>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col l4 m2 s12" v-if="$store.state.user.permisos.includes('user.list') || $store.state.user.permisos.includes('user.add')">
                <div class="row">
                    <div class="col s12 m12" >
                        <div class="card blue-colegio darken-1 unselectable">
                            <div class="card-content white-text text-center">
                                <span class="material-icons" style="font-size: 48px">person</span>
                                <span class="card-title">{{ numero_usuarios }} Usuarios</span>
                            </div>
                            <div class="card-action">
                                <router-link style="color:white;" to="/administracion/usuarios" class="waves-effect btn-flat waves-light"><i class="material-icons left">format_list_bulleted</i>Listado</router-link>
                                <router-link style="color:white;" to="/administracion/nuevo-usuario" class="waves-effect btn-flat waves-light"><i class="material-icons left">person_add</i>Nuevo</router-link>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col l4 m2 s12" v-if="$store.state.user.permisos.includes('aspirante.list') || $store.state.user.permisos.includes('aspirante.add')">
                <div class="row">
                    <div class="col s12 m12">
                        <div class="card blue-colegio darken-1 unselectable">
                            <div class="card-content white-text text-center">
                                <span class="material-icons" style="font-size: 48px">hourglass_empty</span>
                                <span class="card-title">{{ numero_aspirantes }} Aspirantes</span>
                            </div>
                            <div class="card-action">
                                <router-link style="color:white;" to="/administracion/aspirantes" class="waves-effect btn-flat waves-light"><i class="material-icons left">format_list_bulleted</i>Listado</router-link>
                                <!-- <router-link style="color:white;" to="/administracion/aspirante/nuevo" class="waves-effect btn-flat waves-light"><i class="material-icons left">person_add</i>Agregar nuevo</router-link> -->
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col l4 m2 s12" v-if="$store.state.user.permisos.includes('area.list') || $store.state.user.permisos.includes('area.add')">
                <div class="row">
                    <div class="col s12 m12">
                        <div class="card blue-colegio darken-1 sticky-action unselectable">
                            <div class="card-content white-text text-center">
                                <span class="material-icons" style="font-size: 48px">domain</span>
                                <span class="card-title">
                                    {{ numero_areas }} Áreas
                                </span>
                            </div>
                            <div class="card-action">
                                <router-link style="color:white;" to="/administracion/areas" class="waves-effect btn-flat waves-light"><i class="material-icons left">format_list_bulleted</i>Administrar</router-link>
                                &nbsp;
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col l4 m2 s12" v-if="$store.state.user.permisos.includes('asignatura.list') || $store.state.user.permisos.includes('asignatura.add')">
                <div class="row">
                    <div class="col s12 m12">
                        <div class="card blue-colegio darken-1 sticky-action unselectable">
                            <div class="card-content white-text text-center">
                                <span class="material-icons" style="font-size: 48px">domain</span>
                                <span class="card-title">
                                    {{ numero_asignaturas }} Asignaturas y Seminarios
                                </span>
                            </div>
                            <div class="card-action">
                                <router-link style="color:white;" to="/administracion/asignaturas" class="waves-effect btn-flat waves-light"><i class="material-icons left">format_list_bulleted</i>Listado</router-link>
                                <router-link style="color:white;" to="/administracion/nueva-asignatura" class="waves-effect btn-flat waves-light"><i class="material-icons left">add</i>Nueva</router-link>
                                &nbsp;
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col l4 m2 s12" v-if="$store.state.user.permisos.includes('programa.list') || $store.state.user.permisos.includes('programa.add')">
                <div class="row">
                    <div class="col s12 m12">
                        <div class="card blue-colegio darken-1 sticky-action unselectable">
                            <div class="card-content white-text text-center">
                                <span class="material-icons" style="font-size: 48px">playlist_add_check</span>
                                <span class="card-title">
                                    {{ numero_programas }} Programas Escolares
                                </span>
                            </div>
                            <div class="card-action">
                                <router-link style="color:white;" to="/administracion/programas-escolares" class="waves-effect btn-flat waves-light"><i class="material-icons left">format_list_bulleted</i>Listado</router-link>
                                <router-link style="color:white;" to="/administracion/nuevo-programa-escolar" class="waves-effect btn-flat waves-light"><i class="material-icons left">add</i>Nuevo</router-link>
                                &nbsp;
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col l4 m2 s12">
                <div class="row">
                    <div class="col s12 m12" v-if="$store.state.user.permisos.includes('permisos.list') || $store.state.user.permisos.includes('permisos.add')">
                        <div class="card blue-colegio darken-1 sticky-action unselectable">
                            <div class="card-content white-text text-center">
                                <span class="material-icons" style="font-size: 48px">verified_user</span>
                                <span class="card-title">
                                    Permisos
                                </span>
                            </div>
                            <div class="card-action">
                                <router-link style="color:white;" to="/administracion/permisos" class="waves-effect btn-flat waves-light"><i class="material-icons left">format_list_bulleted</i>Listado</router-link>
                                &nbsp;
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col l4 m2 s12">
                <div class="row">
                    <div class="col s12 m12" v-if="$store.state.user.permisos.includes('periodo.list') || $store.state.user.permisos.includes('periodo.add')">
                        <div class="card blue-colegio darken-1 sticky-action unselectable">
                            <div class="card-content white-text text-center">
                                <span class="material-icons" style="font-size: 48px">av_timer</span>
                                <span class="card-title">
                                    {{ cantidad_periodos }} Semestres
                                </span>
                            </div>
                            <div class="card-action">
                                <router-link style="color:white;" to="/administracion/periodos" class="waves-effect btn-flat waves-light"><i class="material-icons left">format_list_bulleted</i>Listado</router-link>
                                &nbsp;
                                <router-link style="color:white;" to="/administracion/agregar-periodo" class="waves-effect btn-flat waves-light"><i class="material-icons left">add</i>Nuevo</router-link>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col l4 m2 s12">
                <div class="row">
                    <div class="col s12 m12" v-if="$store.state.user.permisos.includes('docentes.list') || $store.state.user.permisos.includes('docentes.add')">
                        <div class="card blue-colegio darken-1 sticky-action unselectable">
                            <div class="card-content white-text text-center">
                                <span class="material-icons" style="font-size: 48px">school</span>
                                <span class="card-title">
                                    {{ cantidad_docentes }} Docentes
                                </span>
                            </div>
                            <div class="card-action">
                                <router-link style="color:white;" to="/administracion/docentes" class="waves-effect btn-flat waves-light"><i class="material-icons left">format_list_bulleted</i>Listado</router-link>
                                &nbsp;
                                <router-link style="color:white;" to="/administracion/nuevo-docente" class="waves-effect btn-flat waves-light"><i class="material-icons left">person_add</i>Nuevo</router-link>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { api_url, store } from '../main'
import router from "../router/index"
import M from 'materialize-css'
import Preloader from '../components/Preloader.vue'
export default {
    mounted(){
        this.logged_in = store.state.user.token != "" && localStorage.getItem('token') != null
        this.check_login()
        this.dashboardAdmin()

        if(this.logged_in){
            this.obtenerPeriodoActual()
        }
    },
    components:{
        Preloader
    },
    methods:{
        check_login(){
            if(!this.logged_in){
                router.push('/administracion/login')
            }
            this.uname = store.state.user.name
        },
        async dashboardAdmin() {
            this.isLoading = true
            await fetch(api_url + '/administracion/', {
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
                    this.numero_alumnos = response.cuenta_alumnos
                    this.numero_usuarios = response.cantidad_usuarios
                    this.numero_aspirantes = response.cantidad_aspirantes
                    this.numero_areas = response.cantidad_areas
                    this.numero_asignaturas = response.cantidad_asignaturas
                    this.numero_programas = response.cantidad_programas
                    this.cantidad_periodos = response.cantidad_periodos
                    this.cantidad_docentes = response.cantidad_docentes
                    
                })
            }).finally(() => {
                this.isLoading = false
            })
        },
        async obtenerPeriodoActual(){
            await fetch(api_url + '/administracion/periodos/actual', {
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
                if(response.periodo != undefined && response.periodo.nombre != undefined){
                    this.periodo_actual = response.periodo
                }
                })
            }).finally(() => {this.request_finished=true;})
        }
    },
    data(){
        return {
            numero_alumnos: 0,
            numero_usuarios: 0,
            numero_aspirantes: 0,
            numero_asignaturas: 0,
            isLoading: false,
            numero_areas: 0,
            numero_programas: 0,
            cantidad_periodos: 0,
            cantidad_docentes: 0,
            periodo_actual: {
                nombre: '',
                inicio: '',
                cierre: '',
                descripcion: '',
                activo: false
            }
        }
    },
}
</script>

<style>
    
    
</style>