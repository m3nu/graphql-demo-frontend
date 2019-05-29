<template>
  <div id="app">
    <h2>List all persons</h2>
    <button type="button" @click="getPersons">Get Persons</button>
    <ul>
      <li v-for="person in persons">
        <b>Author #{{ person.id}}</b>: {{ person.name }} - {{ person.age}} years old
        <ul>
          <li v-for="post in person.postSet">{{ post.title }}</li>
        </ul>
      </li>
    </ul>

    <h2>Login to Add Person</h2>
    <input type="text" name="username" placeholder="Username" v-model="username">
    <input type="password" name="password" v-model="password">
    <button type="button" @click="login">Login</button>

    <h2>Add Person</h2>
    <input type="text" name="name" placeholder="Name" v-model="name">
    <input type="number" name="age" min="18" max="100" placeholder="Age" v-model="age">
    <button type="button" @click="addPerson">Add Person</button>
  </div>
</template>

<script>
  import { request, GraphQLClient } from 'graphql-request'

  export default {
    name: 'app',
    data () {
      return {
        graphQLClient: new GraphQLClient('http://localhost:8000/graphql', {
          credentials: 'include',
        }),
        persons: [],
        name: '',
        age: '',
        username: 'demo',
        password: 'demo',
      }
    },
    methods: {
      getPersons () {
        const query = `{
                          allPersons {
                              id, name, age, postSet { title }
                          }
                        }`

        this.graphQLClient.request(query).then(data =>
          this.persons = data.allPersons
        )
      },
      login () {
        const mutation = `mutation login($username: String!, $password: String!) {
          login(username: $username, password: $password) {
            ok
          }
        }`
        this.graphQLClient.request(mutation, {username: this.username, password: this.password})
          .then(data => {
            alert("Successfully logged in.")
            this.username = ''
            this.password = ''
          })
          .catch( (error) => {
            alert(error.response.errors[0].message)
          })
      },
      addPerson () {
        const mutation = `mutation addPerson($name: String!, $age: Int!) {
          createPerson(name: $name, age: $age) {
            person { id, name, age }
          }
        }`
        this.graphQLClient.request(mutation, {name: this.name, age: this.age})
          .then(data => {
            alert(`Added person ${data.createPerson.person.name}.`)
            this.name = ''
            this.age = ''
          })
          .catch(error => {
            alert(error.response.errors[0].message)
          })
      }
    },
  }
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
