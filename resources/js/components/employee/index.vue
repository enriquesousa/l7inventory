<template>
<div>

    <div class="row">
        <router-link to="/store-employee" class="btn btn-primary">Añadir Empleado</router-link>
    </div>
    <br>
    <input type="text" v-model="searchTerm" class="form-control mb-2" style="width: 300px;" placeholder="Buscar aquí">

    <!-- Begin Row Simple Table -->
    <div class="row">
        <div class="col-lg-12 mb-4">
            <!-- Simple Tables -->
            <div class="card">
                <div class="card-header py-3 d-flex flex-row align-items-center justify-content-between">
                    <h6 class="m-0 font-weight-bold text-primary">Lista de Empleados</h6>
                </div>
                <div class="table-responsive">
                    <table class="table align-items-center table-flush">
                        <thead class="thead-light">
                            <tr>
                                <th>Nombre</th>
                                <th>Foto</th>
                                <th>Teléfono</th>
                                <th>Salario</th>
                                <th>Fecha Inicio</th>
                                <th>Acción</th>
                            </tr>
                        </thead>
                        <tbody>

                            <tr v-for="employee in filtersearch" :key="employee.id">
                                <td>{{ employee.name }}</td>
                                <td> <img :src="employee.photo" id="em_photo"></td>
                                <td>{{ employee.phone }}</td>
                                <td>{{ employee.salary }}</td>
                                <td>{{ employee.joining_date }}</td>
                                <td>
                                    <router-link :to="{name: 'edit-employee', params:{id:employee.id}}" class="btn btn-sm btn-primary">Editar</router-link>
                                    <a @click="deleteEmployee(employee.id)" class="btn btn-sm btn-danger"><font color="#ffffff">Borrar</font></a>
                                </td>
                            </tr>
                            
                        </tbody>
                    </table>
                </div>
                <div class="card-footer"></div>
            </div>
        </div>
    </div>
    <!-- End Row Simple Table -->

</div>
</template>

<script type="text/javascript">

export default {

    created(){
        if (!User.loggedIn()){
        this.$router.push({ name: 'home'})
        }
    },

    data(){
        return{
            employees:[],
            searchTerm:'',
        }
    },

    computed:{
        filtersearch(){
            return this.employees.filter(employee => {
                return employee.name.match(this.searchTerm)
                // return employee.phone.match(this.searchTerm)
            })
        }
    },

    methods:{

        allEmployee(){
            axios.get('/api/employee')
            .then(({data}) => (this.employees = data))
            .catch()
        },

        deleteEmployee(id){
            Swal.fire({
            title: 'Estas Seguro?',
            text: "No podrás revertir esto!",
            icon: 'warning',
            showCancelButton: true,
            confirmButtonColor: '#3085d6',
            cancelButtonColor: '#d33',
            confirmButtonText: 'Si, Eliminar!'
            }).then((result) => {
                if (result.isConfirmed) {
                    axios.delete('/api/employee/'+id)
                    .then(() => {
                        this.employee = this.employees.filter(employee => {
                            return employee.id != id
                        })
                    })
                    .catch(() => {
                        this.$router.push({name: 'employee'})
                    })
                    location.reload()
                    Swal.fire(
                    'Borrado!',
                    'El Archivo se ha Eliminado.',
                    'Éxito!'
                    )
                }
            })
        },

    },

    created(){
        this.allEmployee();
    },

}
  
</script>


<style type="text/css">
#em_photo{
    height: 40px;
    width: 40px;
}
</style>