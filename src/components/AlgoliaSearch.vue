<script setup lang="ts">
import algoliasearch from 'algoliasearch/lite'
import { ref } from 'vue'
const users = ['06065605000147', '06181695000131']
const response = await fetch('http://127.0.0.1:3000/api/api-key', {
  method: 'POST',
  body: JSON.stringify({
    cnpj: users[1],
    token: users[1]
  }),
  headers: {
    'Content-Type': 'application/json'
  }
})
const securityApiKey = (await response.json()).apikey
console.log('securityApiKey', securityApiKey)
const searchClient = algoliasearch('D210A1E023', securityApiKey)
const hasPlusDiscount = (product: { plusDiscount: string[] }) => {
  return product.plusDiscount?.some((item) => item === users[1])
}
const filters = ['plusDiscount']
const handleFilters = ref('')
const filterOnCheck = (event: Event, filterName: string) => {
  handleFilters.value = event.target?.checked ? `${filterName}:${users[1]}` : ''
}
</script>

<template>
  <ais-instant-search :search-client="searchClient" index-name="produtos">
    <header>
      <ais-search-box />
      <ais-configure :filters="handleFilters" />
    </header>
    <main class="content">
      <section class="filters">
        <div class="container-header">
          <h2>Filters</h2>

          <ais-clear-refinements data-layout="desktop">
            <template #resetLabel>
              <div class="clear-filters">
                <svg xmlns="http://www.w3.org/2000/svg" width="10" height="10" viewBox="0 0 11 11">
                  <g fill="none" fill-rule="evenodd" opacity=".4">
                    <path d="M0 0h11v11H0z" />
                    <path
                      fill="#000"
                      fill-rule="nonzero"
                      d="M8.26 2.75a3.896 3.896 0 1 0 1.102 3.262l.007-.056a.49.49 0 0 1 .485-.456c.253 0 .451.206.437.457 0 0 .012-.109-.006.061a4.813 4.813 0 1 1-1.348-3.887v-.987a.458.458 0 1 1 .917.002v2.062a.459.459 0 0 1-.459.459H7.334a.458.458 0 1 1-.002-.917h.928z"
                    />
                  </g>
                </svg>
                Clear filters
              </div>
            </template>
          </ais-clear-refinements>

          <ais-stats data-layout="mobile">
            <template #default="{ nbHits }">
              <span class="ais-Stats-text">
                <strong>{{ nbHits }}</strong> results
              </span>
            </template>
          </ais-stats>

          <div class="container-body">
            <ais-panel>
              <template #header> Brands </template>

              <template #default>
                <ais-refinement-list attribute="brand" />
              </template>
            </ais-panel>
            <div>
              <div>Campaigns</div>
              <ul>
                <li v-for="(filter, filterIndex) in filters" :key="`filter-${filterIndex}`">
                  <label :for="`filter-${filterIndex}`"></label>
                  <input
                    :id="`filter-${filterIndex}`"
                    type="checkbox"
                    :value="filter"
                    @change="filterOnCheck($event, filter)"
                  />
                  {{ filter }}
                </li>
              </ul>
            </div>
          </div>
        </div>
      </section>
      <section>
        <ais-hits class="hits">
          <template v-slot:item="{ item }">
            <img :src="item.image" />
            <h4 class="hits__title">{{ item.name }}</h4>
            <div v-if="hasPlusDiscount(item)" class="tag" />
          </template>
        </ais-hits>
      </section>
    </main>
  </ais-instant-search>
</template>

<style lang="scss" scoped>
.content {
  display: flex;
}
.filters {
  width: 180px;
}
.hits {
  :deep(.ais-Hits-list) {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    list-style-type: none;

    .ais-Hits-item {
      align-items: center;
      display: flex;
      height: 328px;
      flex-direction: column;
      padding: 10px;
      width: 208px;
      position: relative;

      img {
        margin-bottom: 10px;
        width: 100px;
      }

      .tag {
        background-color: red;
        height: 10px;
        position: absolute;
        right: 26px;
        top: 0;
        width: 30px;
      }
    }
  }

  &__title {
    font-size: 12px;
    font-weight: 700;
  }
}
</style>
