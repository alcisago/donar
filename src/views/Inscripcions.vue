<template>
<div class="container">
    <h1>Inscripcion</h1>

    <b-alert 
    :show="dismissCountDown" 
    dismissible 
    :variant="mensaje.color" 
    @dismissed="dismissCountDown=0" 
    @dismiss-count-down="countDownChanged" > 
    {{mensaje.texto}} 
    </b-alert>


    <form @submit.prevent="editarInscripcion(inscripcionEditar)" v-if="editar">
        <h3>Editar inscripcion</h3>

        <input type="text" class="form-control my-2" placeholder="Nombre" v-model="inscripcionEditar.nombre">
        <input type="text" class="form-control my-2" placeholder="Descripcion" v-model="inscripcionEditar.descripcion">
        <b-button class="btn-success my-2 mx-2" type="submit">Editar</b-button>
        <b-button class=" my-2" type="submit" @click="editar=false">Cancelar</b-button>

    </form>
    <form @submit.prevent="agregarInscripcion()" v-if="!editar">
        <h3>Agregar una nueva inscripcion</h3>

        <input type="text" class="form-control my-2" placeholder="Nombre" v-model="inscripcion.nombre">
        <input type="text" class="form-control my-2" placeholder="Descripcion" v-model="inscripcion.descripcion">
        <b-button class="btn-success my-2" type="submit">Agregar</b-button>

    </form>

    <table class="table">
        <thead>
            <tr>
                <th scope="col">#</th>
                <th scope="col">Inscripcion</th>
                <th scope="col">Descripcion</th>
                <th scope="col">Acciones</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(item, index) in inscripcions" :key="index">
                <th scope="row">{{item._id}}</th>
                <td>{{item.nombre}}</td>
                <td>{{item.descripcion}}</td>
                <td>
                    <b-button class="btn-danger mx-2" @click="eliminarInscripcion(item._id)">Eliminar</b-button>
                    <b-button class="btn-warning mx-2" @click="activarEdicion(item._id)">Editar</b-button>
                </td>
            </tr>

        </tbody>
    </table>

</div>
</template>

<script>
export default {

    data() {
        return {

            inscripcions: [],
            mensaje: {color: 'success', texto: ''}, 
            dismissSecs: 5, 
            dismissCountDown: 0,

            inscripcion:{nombre:"",descripcion:""},
            editar:false,
            inscripcionEditar:{}

        }

    },

    created() {

        this.listarInscripcions();

    },

    methods: {

        listarInscripcions() {

            this.axios.get('/inscripcion')
                .then(res => {
                    console.log(res.data);
                    this.inscripcions = res.data;

                })
                .catch(e => {

                    console.log(e.response);

                })

        },

        agregarInscripcions(){


            this.axios.post('/nueva-inscripcion',this.inscripcion)
            .then(res=>{

                this.inscripcions.push(res.data)
                this.nota.nombre="";
                this.nota.descripcion="";
                this.mensaje.color="success";
                this.mensaje.texto="Inscripcion Agregada";
                this.showAlert();


            })
            .catch(e=>{

                console.log(e.response);

            })


        },

        eliminarInscripcion(id){

            this.axios.delete(`/inscripcion/${id}`)
            .then(res=>{

                const index = this.inscripcions.findIndex(item=> item._id===res.data._id);
                this.inscripcions.splice(index, 1);
                this.mensaje.color="success";
                this.mensaje.texto="Inscripcion Eliminada";
                this.showAlert();


            })
            .catch(e=>{

                  console.log(e.response);

            })
        },

        activarEdicion(id){

            this.editar=true;
            this.axios.get(`/inscripcion/${id}`)
            .then(res=>{

                this.inscripcionEditar=res.data;

            })
            .catch(e=>{

                 console.log(e.response);


            })


        },

        editarInscripcion(item){
            
            this.axios.put(`/inscripcion/${item._id}`, item)
            .then(res=>{
                const index= this.inscripcions.findIndex(n=> n._id===res.data._id);
                this.inscripcions[index].nombre=res.data.nombre;
                this.inscripcions[index].descripcion=res.data.descripcion;
                this.mensaje.color="success";
                this.mensaje.texto="Inscripcion Editada";
                this.showAlert();
                this.editar=false;


            })
            .catch(e=>{

                console.log(e.response);

            })



        },
        countDownChanged(dismissCountDown) { 
            this.dismissCountDown = dismissCountDown 
        }, 
        showAlert() { 
            this.dismissCountDown = this.dismissSecs 
        }

    }

}
</script>
