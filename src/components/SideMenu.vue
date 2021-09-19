<template>
  <div class="menu" ref="menu" v-on:scroll="handleScroll">
    <div ref="menuItems">
      <div
        class="menu_item"
        v-for="person in allPeople.people"
        :key="person.index"
        v-on:click="handleClick(person.id)"
      >
        <div>
          <div class="name">
            {{ person.name }}
          </div>
          <div class="description">
            {{ person.species ? person.species.name : person.gender }} from {{ person.homeworld.name }}
          </div>
        </div>
        <div class="arrow"></div>
      </div>
    </div>
    <div class="loader" :style="this.showMoreEnabled ? '' : 'display:none;'">
      <div class="lds-spinner"><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
      <span class="loader_text">
        Loading
      </span>
    </div>
  </div>
</template>

<script>
import gql from 'graphql-tag'

export default {
  name: 'SideMenu',
  data() {
    return{
      after: null,
      showMoreEnabled: true,
    }
  },
  updated() {
    this.compareSizeToLoad ? this.handleScroll() : "";
  },
  apollo: {
    allPeople: gql`
      query firstFivePeople($after: String){
        allPeople(first: 5, after: $after){
          people{
            id
            name
            gender
            species{
              name
              classification
            }
            homeworld {
              name
            }
          }
          pageInfo{
            endCursor
          }
        }
      }
    `,
    // Initial variables
    variables: {
      after: null
    }
  },
  methods: {
    loadMore() {
      // Fetch more data and transform the original result
      this.$apollo.queries.allPeople.fetchMore({
        // New variables
        variables: {
          after: this.$apolloData.data.allPeople.pageInfo.endCursor,
        },
        // Transform the previous result with new data
        updateQuery: (previousResult, { fetchMoreResult }) => {
          const newPageInfo = fetchMoreResult.allPeople.pageInfo.endCursor
          if (newPageInfo === null){
              this.showMoreEnabled = false;
          }
          const newPeople = fetchMoreResult.allPeople.people
          const hasMore = fetchMoreResult.allPeople.hasMore

          return {
            allPeople: {
              __typename: previousResult.allPeople.__typename,
              // Merging the tag list
              people: [...previousResult.allPeople.people, ...newPeople],
              hasMore,
              pageInfo: {
                __typename: previousResult.allPeople.pageInfo.__typename,
                endCursor: newPageInfo
              }
            },
          }
        },
      })
    },
    handleClick(id) {
      this.$root.$refs.PersonInfo.handlePerson(id);
    },
    compareSizeToLoad(){
      return this.$refs.menuItems.scrollHeight < this.$refs.menu.offsetHeight || this.$refs.menu.scrollTop + this.$refs.menu.offsetHeight === this.$refs.menu.scrollHeight
    },
    handleScroll() {
      if(this.compareSizeToLoad() && this.showMoreEnabled) {
        this.loadMore()
      }
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .menu{
    border-right: 1px solid #0000001A;
    min-width: 24rem;
    height: calc(100vh - 3.45rem);
    overflow: scroll;
  }
  .menu_item{
    margin-left: 1rem;
    padding: 1rem 2rem 1rem 0;
    font-weight: bold;
    border-bottom: 1px solid #0000001A;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .menu_item .name{
    font-size: 17px;
    color: var(--textDark);
  }
  .menu_item .description{
    font-size: 14px;
    color: var(--textLight);
  }
  .arrow{
    border-top: 2px solid black;
    border-right: 2px solid black;
    width: .5rem;
    height: .5rem;
    transform: rotate(45deg);
  }
  .loader{
    margin-top: 1rem;
    margin-bottom: 1rem;
    font-size: 17px;
    color: var(--textLight);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .loader_text{
    font-weight: bold;
    color: var(--textLight);
  }

  /* LOADER SPINNER */
  .lds-spinner {
    color: official;
    display: inline-block;
    position: relative;
    width: 20px;
    height: 20px;
  }
  .lds-spinner div {
    transform-origin: 7px 7px;
    animation: lds-spinner 1.2s linear infinite;
  }
  .lds-spinner div:after {
    content: " ";
    display: block;
    position: absolute;
    top: -3px;
    right: 12px;
    width: 2px;
    height: 5px;
    border-radius: 20%;
    background: #000;
  }
  .lds-spinner div:nth-child(1) {
    transform: rotate(0deg);
    animation-delay: -1.1s;
  }
  .lds-spinner div:nth-child(2) {
    transform: rotate(30deg);
    animation-delay: -1s;
  }
  .lds-spinner div:nth-child(3) {
    transform: rotate(60deg);
    animation-delay: -0.9s;
  }
  .lds-spinner div:nth-child(4) {
    transform: rotate(90deg);
    animation-delay: -0.8s;
  }
  .lds-spinner div:nth-child(5) {
    transform: rotate(120deg);
    animation-delay: -0.7s;
  }
  .lds-spinner div:nth-child(6) {
    transform: rotate(150deg);
    animation-delay: -0.6s;
  }
  .lds-spinner div:nth-child(7) {
    transform: rotate(180deg);
    animation-delay: -0.5s;
  }
  .lds-spinner div:nth-child(8) {
    transform: rotate(210deg);
    animation-delay: -0.4s;
  }
  .lds-spinner div:nth-child(9) {
    transform: rotate(240deg);
    animation-delay: -0.3s;
  }
  .lds-spinner div:nth-child(10) {
    transform: rotate(270deg);
    animation-delay: -0.2s;
  }
  .lds-spinner div:nth-child(11) {
    transform: rotate(300deg);
    animation-delay: -0.1s;
  }
  .lds-spinner div:nth-child(12) {
    transform: rotate(330deg);
    animation-delay: 0s;
  }
  @keyframes lds-spinner {
    0% {
      opacity: .4;
    }
    100% {
      opacity: 0;
    }
  }
</style>