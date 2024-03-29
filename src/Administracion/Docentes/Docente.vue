<template>
    <div class="container white z-depth-1 mt-4">
        <Preloader v-if="isLoading" />
        <div class="row">
            <nav>
                <div class="nav-wrapper blue-colegio">
                    <div class="col s12">
                        <router-link to="/administracion" class="breadcrumb">Administración</router-link>
                        <router-link to="/administracion/docentes" class="breadcrumb">Docentes</router-link>
                        <router-link :to="'/administracion/docente/' + docente._id " class="breadcrumb">{{ docente.nivel.toUpperCase() }} {{ docente.nombre.toUpperCase() }} {{ docente.primer_apellido.toUpperCase() }} {{ docente.segundo_apellido.toUpperCase() }}</router-link>
                    </div>
                </div>
            </nav>
        </div>
        <div class="row">
            <div class="col s12 m2 center" >
                <img :src="(docente.foto == '' || docente.foto == undefined)  ? require('@/assets/user_placeholder.png') : docente.foto" ref="profileImage" style="width:100%;" :title="docente.nombre" class="circle"/>
                <button v-if="!cambioImagen" ref="imagePicker" class="btn btn-floating right waves-effect" @click.prevent="this.$refs.findFoto.click();" style="position:relative; bottom:4em; right:1em;">
                    <i class="material-icons">add_a_photo</i>
                </button>
                <button v-else class="btn btn-floating right waves-effect green darken-2" @click.prevent="guardarImagen()" style="position:relative; bottom:4em; right:1em;">
                    <i class="material-icons">save</i>
                </button>
                <input type="file" ref="findFoto" v-show="false" @change="seleccionarImagen()"/>
                <br/>
                <div class="row">
                    <div class="col s12 m12 center">
                        <strong>{{ docente.nombre.toUpperCase() }} {{ docente.primer_apellido.toUpperCase() }} {{ docente.segundo_apellido.toUpperCase() }}</strong>
                        <br/>
                        <strong v-if="docente.curp != '' || docente.curp != undefined">{{ docente.curp }}</strong>
                        <p v-else class="red-text">El docente no tiene curp configurada</p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Preloader from "../../components/Preloader.vue"
import { store, api_url } from "../../main"
import router from "../../router/index"
import M from "materialize-css"

export default {
    mounted(){
        this.logged_in = store.state.user.token != "" && localStorage.getItem('token') != null
        this.check_login()
        this.informacionDocente()
    },
    components:{
        Preloader,
    },
    methods:{
        check_login(){
            if(!this.logged_in){
                router.push('/administracion/login')
            }
            this.uname = store.state.user.name
        },
        async informacionDocente(){
            this.isLoading = true
            const docente_id = this.$route.params.id
            await fetch(api_url + '/administracion/docentes/' + docente_id, { 
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
                            M.toast({ html: response.message, classes: 'red darken-2' })
                            return false
                        }
                    }
                    this.docente = response.docente
                })
            }).finally(() => { this.isLoading = false })
        },
        async seleccionarImagen(){
            this.isLoading = true
            this.$refs.profileImage.src =  await this.base64Photo()
            this.cambioImagen = true
        },
        base64Photo(){
            const ctx = this
            return new Promise((resolve, reject) =>{
                const reader = new FileReader();
                reader.readAsDataURL(this.$refs.findFoto.files[0])
                reader.onload = () => {
                    resolve(reader.result)
                    ctx.isLoading =  false
                }
                reader.onerror = error => {
                    reject(error)
                    ctx.isLoading =  false
                }
            })
        },
    },
    data() {
        return { 
            isLoading: false,
            docente: {
                nombre: '',
                primer_apellido: '',
                segundo_apellido: '',
                curp: '',
                nivel: '',
            },
            cambioImagen: false
        }
    }
}
</script>

<style>

</style>