<template>
  <div class="hello">
    <div class="header">
      Ravn Star Wars Registry
    </div>
    <div class="hero">
      <SideMenu/>
      <div class="content">
        General Information
        <!-- <div class="details"></div> -->
      </div>
    </div>
  </div>
</template>

<script>
import SideMenu from "./SideMenu.vue"

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  components: {
    SideMenu
  },
  data() {
    return {
      after: null,
      showMoreEnabled: true,
    }
  },
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
