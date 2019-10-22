<template>
  <div id="app">
    <h1>KonaSoft App</h1>
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <div>
      <form>
        <div class="form-group">
          <label for="searchQuery">Search Text:</label>
          <input
            name="seach"
            type="text"
            class="form-control"
            id="searchQuery"
            aria-describedby="searchQuery"
            placeholder="Enter search term"
            v-model="searchQuery"
          />
          <small id="searchHelp" class="form-text text-muted">Filter users by entering search term</small>
        </div>
      </form>
    </div>
    <h3 v-if="error">{{ error }}</h3>

    <TableDisplay
      @showModal="onShowModal"
      v-bind:users="users"
      v-bind:headers="headers"
      v-bind:columns="columns"
      v-bind:filter-key="searchQuery"
      v-else
    ></TableDisplay>
    <Modal @showModal="onShowModal" v-if="showModal"></Modal>
  </div>
</template>

<script>
import TableDisplay from "./components/TableDisplay.vue";
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
      searchQuery: "",
      headers: [],
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
    filterUsers() {},
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

      // assign original DB user keys as table headers
      this.headers = Object.keys(users[0]);

      // use mapped properties for users and column data
      this.users = this.mapToEnglish(users);
      this.columns = Object.keys(this.users[0]);
      console.log("headers", this.headers, "cols", this.columns);
    } catch (error) {
      // TODO - error handling gracefully
      this.error = error;
    }
  },
  components: {
    TableDisplay
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
  form {
    width: 50%;
    margin: 0 auto;
  }
}
</style>
