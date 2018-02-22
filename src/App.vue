<template>
	<div>
  <!-- Navigation-->
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-top" id="mainNav">
    <a class="navbar-brand" href="index.html">Sistema IPEA Datalab</a> 
  </nav>
  <div class="content-wrapper">
    <div class="container-fluid">
      <!-- Breadcrumbs-->
      <ol class="breadcrumb">
        <li class="breadcrumb-item">
          <a href="#">Dashboard</a>
        </li>
        <li class="breadcrumb-item active">Cadastro de Pessoal</li>
      </ol>
      <!-- Icon Cards-->
	  
      <div class="row top-options">
        <div class="col-xl-6 col-sm-6 mb-6 centered-content">
          <div class="card text-white bg-primary o-hidden h-100">
            <div class="card-body">
              
              <div class="mr-5">Cadastro manual de Pessoa</div>
			  <div class="mr-5">
				<input placeholder="Primeiro Nome" v-model="newPersonName" />
				<input placeholder="Sobrenome" v-model="newPersonSurname" />
			  </div>
			  <div class="mr-5" style="margin-top:10px">
				<button @click="addPerson()">Cadastrar</button>
			  </div>
            </div>
          </div>
        </div>  
		<div class="col-xl-6 col-sm-6 mb-6 centered-content">
          <div class="card text-white bg-primary o-hidden h-100">
            <div class="card-body">
				<button @click="addPersonRemotely()">Cadastro Automático de Pessoa</button>
				<br>
				<div v-show="isLoading">
					Por favor, aguarde...<br>
					<i class="fa fa-cog fa-spin"></i>
				</div>
            </div>
          </div>
        </div>  		
      </div>
	  <div class="row">
		<div class="col-sm-12">
      <!-- Example DataTables Card-->
		  <div class="card mb-3">
			<div class="card-header">
			  <i class="fa fa-table"></i> Pessoas Cadastradas</div>
			<div class="card-body">
			  <div class="table-responsive">
				<table class="table table-bordered" id="dataTable" width="100%" cellspacing="0">
				  <thead>
					<tr>
					  <th>Nome</th>
					  <th>Sobrenome</th>
					</tr>
				  </thead>
				  <tbody>
					<tr v-for="person in people">
					  <td>{{person.firstname}}</td>
					  <td>{{person.surname}}</td>
					</tr>
				  </tbody>
				</table>
			  </div>
			</div>
			<div class="card-footer small text-muted centered-content" >
				<button @click="persistData()">Persistir Dados Localmente</button>
				<button @click="loadPersistedData()">Carregar Dados</button>
			</div>
		  </div>
		</div>
	  </div>
    </div>
	
    <!-- /.container-fluid-->
    <!-- /.content-wrapper-->
    <footer class="sticky-footer">
      <div class="container">
        <div class="text-center">
          <small>Copyright © IPEA Datalab Test 2018 - By Felipe F. Soares</small>
        </div>
      </div>
    </footer>
    <!-- Scroll to Top Button-->
    <a class="scroll-to-top rounded" href="#page-top">
      <i class="fa fa-angle-up"></i>
    </a>
    
  </div>
  </div>
</template>

<script>
import Vue from 'vue'
import VueLocalStorage from 'vue-localstorage'
Vue.use(VueLocalStorage)
export default {
  name: 'app',
  data () {
    return {
      people: [],
	  newPersonName: "",
	  newPersonSurname: "",
	  isLoading: false
    }
  },
  methods: {
	addPerson: function () {
		if (this.newPersonName.length < 1 || this.newPersonSurname.length < 1 ) {
			alert('Por favor insira nome/sobrenome válido')
			return;
		}
		this.people.push({firstname: this.newPersonName, surname: this.newPersonSurname})
		this.newPersonName = ""
		this.newPersonSurname = ""
	},
	addPersonRemotely () {
		this.isLoading = true
		self = this
		Vue.http.get("https://randomuser.me/api").then(
         response=>{
			let person = response.data.results[0]
			self.people.push({firstname: person.name.first , surname: person.name.last})
         },
         error=>{
           console.log(error)
         }).finally(function () {
			self.isLoading = false
        })
	},
	persistData () {
		Vue.localStorage.set('appData', JSON.stringify(this.people))
		alert('Dados salvos com sucesso!')
	},
	loadPersistedData () {
		let appData = Vue.localStorage.get('appData')
		if (appData) {
			this.people = JSON.parse(appData)
		}
	}
  }
}
</script>
<style>

	.row {
		margin-bottom: 20px;
	}
	
	.centered-content {
		text-align: center
	}
	
	footer.sticky-footer {
		width: 100%;
	}

	#mainNav .navbar-brand {
		width: 100%;
		text-align: center;
	}
	
	.bg-primary {
		background-color: #aaa !important;
	}
	
	.content-wrapper {
		 margin-left: 0px;
	}
</style>
