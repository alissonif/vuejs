<template>
  <MainHeader />
  <div class="users">
    <div class="container">
      <section>
        <h5 class="title">Lista de usuários</h5>
        <form @submit="createUser">
          <input type="text" placeholder="Nome" v-model="form.name" />
          <input type="email" placeholder="E-mail" v-model="form.email" />
          <button type="submit">Adicionar</button>
        </form>
        <ul>
          <li v-for="user in users" :key="user.id">
            <p>{{ user.name }}</p>
            <small>{{ user.email }}</small>
            <a class="destroy" @click="destroyUser(user.id)"> X </a>
          </li>
        </ul>
      </section>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import MainHeader from '@/MainHeader.vue'
import axios from '@/utils/axios'

interface User {
  id: string
  name: string
  email: string
}
export default defineComponent({
  components: {
    MainHeader,
  },
  data() {
    return {
      users: [] as User[],
      form: {
        name: '',
        email: '',
      },
    }
  },
  created() {
    this.fetchUsers()
  },
  methods: {
    async fetchUsers() {
      try {
        const { data } = await axios.get('/users')
        this.users = data
      } catch (error) {
        console.warn(error)
      }
    },
    async createUser() {
      try {
        const { data } = await axios.post('/users', this.form)
        this.users.push(data)
        this.form = {
          name: '',
          email: '',
        }
      } catch (error) {
        console.warn(error)
      }
    },
    async destroyUser(id: User['id']) {
      try {
        await axios.delete(`/users/${id}`)
        const userIndex = this.users.findIndex((user) => user.id === id)
        this.users.splice(userIndex, 1)
      } catch (error) {
        console.warn(error)
      }
    },
  },
})
</script>
