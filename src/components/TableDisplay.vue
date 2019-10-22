<template>
  <div class="table">
    <div class="thead">
      <div v-for="column in columns" class="tcell">{{ column }}</div>
    </div>
    <div class="tbody">
      <div class="trow" @click="editRow" v-for="user in filteredUsers" :key="user.id">
        <div class="tcell" v-for="value in user">{{ value }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "TableDisplay",
  props: {
    users: Array,
    columns: Array,
    filterKey: String
  },
  methods: {
    editRow(event) {
      this.$emit("showModal", event.target.p);
      console.log(event);
    },
    sortBy(key) {
      this.sortKey = key;
      this.sortOrders[key] = this.sortOrders[key] * -1;
    }
  },
  data: function() {
    var sortOrders = {};
    this.columns.forEach(function(key) {
      sortOrders[key] = 1;
    });
    return {
      sortKey: "",
      sortOrders: sortOrders
    };
  },
  computed: {
    filteredUsers: function() {
      debugger;
      var sortKey = this.sortKey;
      var filterKey = this.filterKey && this.filterKey.toLowerCase();
      var order = this.sortOrders[sortKey] || 1;
      var users = this.users;
      if (filterKey) {
        users = users.filter(function(row) {
          return Object.keys(row).some(function(key) {
            return (
              String(row[key])
                .toLowerCase()
                .indexOf(filterKey) > -1
            );
          });
        });
      }
      if (sortKey) {
        users = users.slice().sort(function(a, b) {
          a = a[sortKey];
          b = b[sortKey];
          return (a === b ? 0 : a > b ? 1 : -1) * order;
        });
      }
      return users;
    }
  }
};
</script>

<style lang="scss" scoped>
* {
  box-sizing: border-box;
}

.table {
  width: 76%;
  height: 100%;
  margin: 0 auto;
}

.table .thead {
  .tcell {
    height: 5em;
  }
}

.thead,
.trow {
  display: flex;
  align-content: space-evenly;
  background-color: #ffffff;
}

.trow {
  &:hover {
    background: rgba(255, 255, 255, 0.7);
    cursor: pointer;
    .tcell {
      background-color: #ffffff;
      // border: 2px solid #ebebeb;
    }
  }
}

.tcell {
  display: flex;
  justify-content: center;
  flex-direction: row;
  align-items: center;
  width: 10em;
  height: 4em;
  margin: 0.1em;
  border: 2px solid #ebebeb;
}
</style>