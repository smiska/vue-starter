<template>
  <div id="app">
    <h1>KonaSoft App</h1>
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <h3 v-if="error">{{ error }}</h3>
    <TableDisplay
      @showModal="onShowModal"
      v-bind:users="users"
      v-bind:columns="columns"
      v-bind:filterText="filterText"
      v-else
    ></TableDisplay>
    <Modal @showModal="onShowModal" v-if="showModal"></Modal>
  </div>
</template>

<script>
import TableDisplay from "./components/TableDisplay.vue";
import Modal from "./components/Modal.vue";
import axios from "axios";

const ITEM = {
  ID: "Id",
  NAME: "Nev",
  PERM: "Jogosultságok",
  AGE: "Eletkor",
  CREATED_AT: "Regisztralt",
  COMPANY: "Munkahely"
};

export default {
  name: "app",
  data() {
    return {
      filterText: "",
      columns: [],
      users: [],
      error: "",
      showModal: false
    };
  },
  methods: {
    onShowModal() {
      this.showModal = !this.showModal;
    },
    mapToEnglish(users) {
      return users.map(user => {
        this.validateUser(user);

        function extractInfo(items) {
          if (!Array.isArray(items)) {
            items = [items];
          }
          return items.map(i => i[ITEM.NAME]);
        }

        return (user = {
          id: user[ITEM.ID],
          name: user[ITEM.NAME],
          permissions:
            typeof user[ITEM.PERM] === "object"
              ? extractInfo(user[ITEM.PERM])
              : user[ITEM.PERM],
          age: user[ITEM.AGE],
          createdAt: user[ITEM.CREATED_AT],
          company:
            typeof user[ITEM.COMPANY] === "object"
              ? extractInfo(user[ITEM.COMPANY])
              : user[ITEM.COMPANY]
        });
      });
    },
    validateUser(user) {
      if (!(user["Id"] && user["Nev"])) {
        throw new Error("Invalid DB data"); // throwing error in for loop?
      }
    }
  },

  async mounted() {
    try {
      const response = await axios.get("./db.json");
      const users = response.data;

      // assign original DB user keys
      this.columns = Object.keys(users[0]);

      // use mapped properties for users
      this.users = this.mapToEnglish(users);
    } catch (error) {
      // TODO - error handling gracefully
      this.error = error;
    }
  },
  components: {
    TableDisplay,
    Modal
  }
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding-top: 60px;

  h1 {
    margin-bottom: 30px;
  }
}
</style>
