<template>
  <div class="table">
    <div class="thead">
      <div v-for="column in columns" class="tcell">{{ column }}</div>
    </div>
    <div class="tbody">
      <div class="trow" @click="editRow" v-for="user in users" :key="user.id">
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
    filterText: String
  },
  methods: {
    editRow(event) {
      this.$emit("showModal", event.target.p);
      console.log(event);
    }
  },
  components: {}
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