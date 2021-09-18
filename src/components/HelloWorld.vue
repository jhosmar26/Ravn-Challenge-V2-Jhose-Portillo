<template>
  <div class="hello">
    <div class="header">
      Ravn Star Wars Registry
    </div>
    <div class="hero">
      <div class="items"></div>
      <div class="content">
        <div v-for="e in allPeople.people" :key="e.index">
          {{ e.name }}
        </div>
        <a v-on:click.prevent="loadMore">aaaa</a>
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
  data() {
    return {
      after: null,
      showMoreEnabled: true,
    }
  },
  apollo: {
    allPeople: gql`
      query firstFivePeople($after: String){
        allPeople(first: 5, after: $after){
          people{
            name
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
          const newPeople = fetchMoreResult.allPeople.people
          const hasMore = fetchMoreResult.allPeople.hasMore

          this.showMoreEnabled = hasMore

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
