<template>
<div class="card">
    <div class="card-header">
        <h4>{{ title }}</h4>        
    </div>
    <div class="card-body">
        <div class="row">
            <div class="col-12">
                <label for="paginate">Registros a mostrar</label>
                <select name="paginate" id="paginate" v-model="size" @change="navigate">
                    <option value="5">5</option>
                    <option value="10">10</option>
                    <option value="25">25</option>
                </select>
            </div>
        </div>
        <table class="table table-hover">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Nombre</th>
                    <th>Email</th>
                    <th>Empresa</th>
                    <th>Acciones</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="user in users.data">
                    <td>{{ user.id }}</td>
                    <td>{{ user.name }}</td>
                    <td>{{ user.email }}</td>
                    <td>{{ user.company }}</td>
                    <td></td>
                </tr>
            </tbody>
        </table>
        <h4>Previo {{prevPage}}</h4>
        <h4>Actual {{currentPage}}</h4>
        <h4>Siguiente {{nextPage}}</h4>
    </div>
    <div class="card-footer">
        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li class="page-item" :class="{'disabled' : !prevPage}">
                    <a class="page-link" href="#" tabindex="-1">Previous</a>
                </li>
                <li class="page-item" v-if="prevPage">
                    <a class="page-link" href="#" @click.prevent="changePage(prevPage ? prevPage : currentPage)">{{ prevPage ? prevPage : currentPage }}</a>
                </li>
                <li class="page-item">
                    <a class="page-link" href="#" @click.prevent="changePage(prevPage ? currentPage : nextPage)">{{ prevPage ? currentPage : nextPage }}</a>
                </li>
                <li class="page-item" v-if="nextPage">
                    <a class="page-link" href="#" @click.prevent="changePage(currentPage > 1 ? nextPage : currentPage + 2)">{{ currentPage > 1 ? nextPage : currentPage + 2 }}</a>
                </li>
                <li class="page-item" :class="{'disabled' : !nextPage}">
                    <a class="page-link" href="#" tabindex="-1">Next</a>
                </li>
            </ul>
        </nav>
    </div>
</div>
</template>

<script>
    export default {
        created() {
            this.navigate();
        },
        data() {
            return {
                size : 25,
                title: '',
                users: [],
                currentPage: null,
                lastPage : null
            }
        },
        computed: {
            nextPage() {
                return this.currentPage < this.lastPage ? (this.currentPage + 1) : null;
            },
            prevPage() {
                return this.currentPage > 1 ? (this.currentPage - 1) : null;
            },
        },
        methods : {
            navigate() {
                var vm = this;
                axios.get("/users/"+ vm.size)
                .then(function (response) {
                    vm.users = response.data;
                    vm.currentPage = vm.users.current_page;
                    vm.lastPage = vm.users.last_page;
                })
                .catch(function (error) {
                    console.log(error);
                })
                .then(function () {
                    // always executed
                });  
            },
            changePage(page) {
                var vm = this;
                axios.get("/users/"+ vm.size, {
                    params: {
                      page: page
                    }
                })
                .then(function (response) {
                    vm.users = response.data;
                    vm.currentPage = vm.users.current_page;
                    vm.lastPage = vm.users.last_page;
                })
                .catch(function (error) {
                    console.log(error);
                })
                .then(function () {
                    // always executed
                }); 
            }
        }
    }
</script>
