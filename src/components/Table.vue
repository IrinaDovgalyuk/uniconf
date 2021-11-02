<template>
  <div class="users">
    <h1>{{ title }}</h1>
    <table class="users__table table" cellpadding="10" cellspacing="0">
      <thead>
        <tr>
            <th class="table__columnId column">
              <span @click="doSort('id')" class="column__name">Id</span>
            </th>
            <th class="table__columnUserId column">
              <span @click="doSort('userId')" class="column__name">UserId</span>
            </th>
            <th class="table__columnTitle column">
              <span @click="doSort('title')" class="column__name">Title</span>
            </th>
            <th class="table__columnBody column">
              <span @click="doSort('body')" class="column__name">Body</span>
            </th>
        </tr>
    </thead>
    <tbody>
      <tr v-for="user in paginatedUsers" :key="user.id">
        <td>{{user.id}}</td>
        <td>{{user.userId}}</td>
        <td>{{user.title}}</td>
        <td>{{user.body}}</td>
    </tr>
    </tbody>
    </table>
    <div class="users__pagination pagination">
      <div class="pagination__page" @click="pageClick()">Загрузить еще</div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
  name: 'Table',
  props: {
    msg: String,
  },
  data() {
    return {
      sort: {
        field: '',
        desc: false,
      },
      usersPerPage: 10,
      pageNumber: 1,
      title: 'Таблица с данными',
      urlUsers: 'https://jsonplaceholder.typicode.com/posts?_limit=50',
      users: [],
      errors: [],
      counter: 1,
    };
  },
  async mounted() {
    await this.getUsers();
  },
  computed: {
    sortedData() {
      if (!this.sort.field) {
        return this.users;
      }
      return this.users.concat().sort((a, b) => {
        if (this.sort.desc) {
          return a[this.sort.field] > b[this.sort.field] ? 1 : -1;
        }
        return a[this.sort.field] > b[this.sort.field] ? -1 : 1;
      });
    },
    pages() {
      return Math.ceil(this.sortedData.length / 10);
    },
    paginatedUsers() {
      const userStart = 0;
      const from = (this.pageNumber - 1) * this.usersPerPage;
      const to = from + this.usersPerPage;
      return this.sortedData.slice(userStart, to);
    },
  },
  methods: {
    async getUsers() {
      try {
        const response = await fetch(this.urlUsers);
        this.users = await response.json();
      } catch (error) {
        this.errors.push(error);
      }
    },
    doSort(field) {
      if (field === this.sort.field) {
        this.sort.desc = !this.sort.desc;
      } else {
        this.sort.field = field;
        this.sort.desc = true;
      }
    },
    pageClick() {
      this.counter += 1;
      this.pageNumber = this.counter;
    },
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1{
  color: #446344;
  padding: 0 0 40px 0;
}
.users__table{
  margin: 0 auto;
  width: 80%;
  border: 1px solid #689568;
  text-align: left;
  font-size: 16px;
}

.users__table th {
  background-color: #99CC99;
  color:#FFFFFF;
  text-align: center;
}

.table__columnBody {
  width: 70%;
}

.column__name {
  cursor: pointer;
  font-size: 20px;
}

.column__name:hover {
  color: #446344;
}

.users__table td,
.users__table th {
  border: 1px solid #689568;
}

.users__pagination {
  width: 250px;
  margin: 0 auto;
  padding: 20px 0;
}

.pagination__page {
  border: 1px solid #689568;
  border-radius: 5px;
  background-color: #FFFFFF;
  padding: 10px 0;
  margin: 15px 0;
  color: #689568;
}

.pagination__page:hover{
  background-color: #99CC99;
  color:#FFFFFF;
  cursor: pointer;
}
</style>
