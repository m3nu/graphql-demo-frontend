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

    <h2>Add Person</h2>
    <input type="text" name="name" placeholder="Name" v-model="name">
    <input type="number" name="age" min="18" max="100" placeholder="Age" v-model="age">
    <button type="button" @click="addPerson">Add Person</button>
  </div>
</template>

<script>
  import { request } from 'graphql-request'

  export default {
    name: 'app',
    data () {
      return {
        graphqlEndpoint: 'http://localhost:8000/graphql',
        persons: [],
        name: '',
        age: '',
      }
    },
    methods: {
      getPersons () {
        const query = `{
                          allPersons {
                              id, name, age, postSet { title }
                          }
                        }`

        request(this.graphqlEndpoint, query).then(data =>
          this.persons = data.allPersons
        )
      },
      addPerson () {
        const mutation = `mutation addPerson($name: String!, $age: Int!) {
          createPerson(name: $name, age: $age) {
            person { id, name, age }
          }
        }`
        request(this.graphqlEndpoint, mutation, {name: this.name, age: this.age}).then(data => {
          alert(`Added person ${data.createPerson.person.name}.`)
          this.name = ''
          this.age = ''
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
