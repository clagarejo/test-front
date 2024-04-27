<template>
  <div>
    <Navbar />

    <main class="mt-5 container">
      <div class="d-flex justify-content-between">
        <div class="space-buttons">
          <button
            type="button"
            class="btn btn-success py-2 px-3"
            @click.prevent="openModal('#create_count')"
          >
            <font-awesome-icon icon="plus" />
            Crear cuenta de ahorros
          </button>
        </div>

        <div class="">
          <button
            type="button"
            class="btn btn-outline-success py-2 px-3 button-border"
            @click.prevent="openModal('#register_count')"
          >
            <font-awesome-icon icon="suitcase" class="close" />

            Movimientos
          </button>
        </div>
      </div>

      <table class="table mt-3 text-center">
        <thead>
          <tr>
            <th scope="col">Número de cuenta</th>
            <th scope="col">Nombre de usuario</th>
            <th scope="col">Saldo</th>
            <th scope="col">Ultimo movimiento</th>
            <th scope="col">Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(account, index) in accounts" :key="index">
            <td> {{ account.id }} </td>
            <td> {{ account.user_name }} </td>
            <td> {{ account.total_amount.toLocaleString() }} </td>
            <td> {{ account.transaction_type_name }} </td>

            <td>
              <a
                class="size-icon btn btn-danger"
                @click.prevent="deleteCount(index)"
              >
                <font-awesome-icon icon="trash-alt" />
              </a>
            </td>
          </tr>
        </tbody>
      </table>
    </main>

    <create-count :closeModal="closeModal" :users="users" />
    <register-count :closeModal="closeModal" />
  </div>
</template>

<script>
import { Modal } from "bootstrap";
import axios from "axios";

import CreateCount from "./modals/CreateCount.vue";
import RegisterCount from "./modals/RegisterCount.vue";

export default {
  components: { CreateCount, RegisterCount },

  data() {
    return {
      modalOpen: null,
      accounts: [],
      users: [],
    };
  },

  mounted() {
    this.getAccounts()
  },

  methods: {
    getAccounts() {
      axios
        .get("http://localhost:8000/api/accounts")
        .then((response) => {
          this.accounts = response.data;
        })
        .catch((error) => {
          console.error("Error al obtener los usuarios:", error);
        });
    },

    getUsers() {
      axios
        .get("http://localhost:8000/api/users")
        .then((response) => {
          this.users = response.data;
          console.log(this.users, 'usuarios')

        })
        .catch((error) => {
          console.error("Error al obtener los usuarios:", error);
        });
    },

    deleteCount(id) {
      axios.delete(`http://localhost:8000/api/accounts/${id}`)
        .then(response => {
          console.log('Tipos de documentos consultados');
          this.getAccounts();
        })
        .catch(error => {
          console.error('Error al eliminar usuario:', error);
        });
    },

    openModal(id) {
      if(id === "#create_count") {
        this.getUsers()
      }
      const modalId = id.startsWith("#") ? id.slice(1) : id;
      const modal = document.getElementById(modalId);
      this.modalOpen = new Modal(modal);

      this.modalOpen.show();
    },

    closeModal() {
      this.modalOpen.hide();
      this.getAccounts()
    },
  },
};
</script>

<style scoped>
.button-border {
  border: 2px solid;
}
</style>