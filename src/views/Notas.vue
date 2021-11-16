<template>
    <div class="container">
        <h1>Notas</h1>
        <b-alert
            :show="dissmissCountDown"
            dismisable
            :variant="mensaje.color"
            @dismissed="dissmissCountDown = 0"
            @dismiss-count-down="countDownChanged"
        >
            {{ mensaje.texto }}
        </b-alert>
        <form @submit.prevent="agregarNota()" v-if="agregar">
            <h3 class="text-center">Agregar nueva nota</h3>
            <input type="text" placeholder="Ingresa el nombre" class="form-control my-2" v-model="nota.nombre">
            <input type="text" placeholder="Ingresa la descripcion" class="form-control my-2" v-model="nota.descripcion">
            <!-- <input type="date" placeholder="Ingresa la fecha" class="form-control my-2" v-model="nota.fecha"> -->
            <b-button class="btn-sm btn-block btn-success" type="submit">Agregar</b-button>
        </form>
        <table class="table">
            <thead>
            <th scope="col">#</th>
            <th scope="col">Nombre</th>
            <th scope="col">Descripcion</th>
            <th scope="col">Fecha</th>
            <th scope="col">Acciones</th>
            </thead>
            <tbody>
            <tr v-for="(item, index) in notas" :key="index">
                <td scope="row">{{ item._id}}</td>
                <td>{{ item.nombre }}</td>
                <td>{{ item.descripcion }}</td>
                <td>{{ item.date }}</td>
                <td>
                    <b-button class="btn btn-warning btn-sm mx-2" @click="activarEdicion(item._id)">
                        Actualizar
                    </b-button>
                    <b-button class="btn btn-danger btn-sm mx-2" @click="eliminarNota(item._id)">
                        Eliminar
                    </b-button>
                </td>
            </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
export default {
    data(){
        return{
            notas: [],
            mensaje: {color: 'success', texto: ''},
            dismissSecs: 3,
            dissmissCountDown: 0,
            nota: {},
            agregar: true,

        }
    },
    created(){
        this.listarNotas();
    },
    methods: {
        listarNotas(){
            this.axios.get('/nota').then((response) => {
                this.notas = response.data;
            })
            .catch(error => {
                console.log(error);
            });
        },
        agregarNota(){
            this.axios.post('/nueva-nota', this.nota).then(res => {
                this.notas.push(res.data);
                this.nota.nombre = '';
                this.nota.descripcion = '';
                // this.nota.fecha = '';   
                // console.log(this.showAlert());
                this.mensaje.texto = 'Nota agregada correctamente';
                this.mensaje.color = 'success';
                this.showAlert();

            }).catch(e => {
                this.mensaje.color = 'danger';
                this.mensaje.texto = 'Ingrese todos los datos';
                this.showAlert();
                })
        },
        eliminarNota(id){
            this.axios.delete(`/nota/${id}`).then(res => {
                const index = this.notas.findIndex(item => item._id == res.data._id);
                this.notas.splice(index, 1);
                this.mensaje.texto = 'Nota eliminada correctamente';
                this.mensaje.color = 'success';
                this.showAlert();
            })
            .catch(e => {
                this.mensaje.color = 'danger';
                this.mensaje.texto = 'Error al eliminar la nota';
                this.showAlert();
            })
        },

        countDownChanged(dissmissCountDown){
            this.dissmissCountDown = dissmissCountDown;
        },
        showAlert(){
            this.dissmissCountDown = this.dismissSecs;
        },
}
}
</script>