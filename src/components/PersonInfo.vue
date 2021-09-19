<template>
  <div v-if="showed" class="person_info">
    <div class="title">General Information</div>
    <div class="subtitle">
      <span>Eye Color</span>
      <span>{{ person.eyeColor }}</span>
    </div>
    <div class="subtitle">
      <span>Hair Color</span>
      <span>{{ person.hairColor }}</span>
    </div>
    <div class="subtitle">
      <span>Skin Color</span>
      <span>{{ person.skinColor }}</span>
    </div>
    <div class="subtitle">
      <span>Birth Year</span>
      <span>{{ person.birthYear }}</span>
    </div>

    <div v-if="vehicles.length > 0">
      <div class="title">Vehicles</div>
      <div class="subtitle" v-for="vehicle in vehicles" :key="vehicle.index">
        <span>{{ vehicle.name }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import gql from 'graphql-tag'

export default ({
  name: 'PersonInfo',
  data() {
    return{
      showed: false,
      person: '',
      vehicles: ""
    }
  },
  // apollo: {
  //   person: gql`
  //     query personById($id: ID!){
  //       person(id: $id){
  //         id
  //         name
  //       }
  //     }
  //   `,
  //   // Initial variables
  //   variables: {
  //     id: "cGVvcGxlOjI="
  //   }
  // },
  created() {
    this.$root.$refs.PersonInfo = this;
  },
  methods: {
    handlePerson(id) {
      this.$apollo.query({
        query: gql `
          query byId($id: ID!) {
            person(id: $id) {
              id
              eyeColor
              hairColor
              skinColor
              birthYear
              vehicleConnection{
                vehicles{
                  name
                }
              }
            }
          }
        `,
        variables: {
          id: id
        }
      }).then(data => {
        this.person = { ...data.data.person }
        this.vehicles = data.data.person.vehicleConnection.vehicles
        });
      this.showed = true;
    }
  }
})
</script>

<style scoped>
  .person_info{
    margin-left: 5rem;
    margin-right: 5rem;
    width: 100%;
  }
  .title{
    font-size: 17px;
    font-weight: bold;
    color: var(--textDark);
    margin-top: 2rem;
    margin-bottom: .2rem;
  }
  .subtitle{
    font-size: 17px;
    font-weight: bold;
    display: flex;
    justify-content: space-between;
    border-bottom: 1px solid #0000001A;
    padding-top: 1rem;
    padding-bottom: 1rem;
  }
  .subtitle span:first-child{
    color: var(--textLight);
  }
  .subtitle span:nth-child(2){
    color: var(--textDark);
  }
</style>
