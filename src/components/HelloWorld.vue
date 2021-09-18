<template>
  <div class="hello">
    <div class="header">
      Ravn Star Wars Registry
    </div>
    <div class="hero">
      <div class="items"></div>
      <div class="content">
        <div v-for="e in allPeople" :key="e.id">
          {{ e.name }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import gql from "graphql-tag";

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  apollo: {
    allPeople: gql`
      query {
        allPeople{
          people{
            name
            birthYear
            id
          }
        }
      }
    `
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .header{
    background-color: black;
    color: white;
    padding-top: 1rem;
    padding-bottom: 1rem;
    padding-left: 2rem;
    font-size: 17px;
    font-weight: bold;
  }
  .hero{
    display: flex;
  }
</style>
