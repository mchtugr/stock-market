<template>
  <div>
    <div class="ma-5" align="center">
      <v-form class="search-form" @submit.prevent="searchCompany">
        <!-- search input -->
        <v-text-field
          v-model="search"
          label="Company Name"
          clearable
          class="search-input"
        ></v-text-field>
        <!-- search btn -->
        <v-btn type="submit">Search</v-btn>
      </v-form>
      <!-- found companies in alpha vantage api -->
      <v-col class="company-list" v-if="symbols.length > 0 && showList">
        <v-list two-line subheader dense>
          <v-subheader>Select a company to view stock data</v-subheader>
          <v-list-item
            v-for="(company, i) in symbols"
            :key="i"
            @click="goToChartPage(company)"
          >
            <v-list-item-content>
              <v-list-item-title>{{ company['2. name'] }}</v-list-item-title>
              <v-list-item-subtitle>{{
                company['4. region']
              }}</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-col>
    </div>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'
export default {
  name: 'Home',
  data() {
    return {
      search: '',
      showList: false,
    }
  },
  computed: {
    ...mapState(['symbols']),
  },
  methods: {
    ...mapActions(['searchSymbols', 'getDailyData']),
    searchCompany() {
      this.searchSymbols(this.search)
      this.showList = true
    },
    // route to chart display page
    goToChartPage(company) {
      const companySymbol = company['1. symbol']
      // fetch target company data
      this.getDailyData(companySymbol)
      this.$router.push({
        path: `/charts/${companySymbol}/daily`,
        query: { company: company['2. name'] },
      })
    },
  },
  beforeDestroy() {
    console.log('hi')
    this.showList = false
  },
}
</script>

<style>
.search-form {
  max-width: 700px;
  display: flex;
  align-items: center;
  padding: 20px 40px 0;
}

.company-list {
  text-align: left;
  max-width: 700px;
  padding: 0 40px;
}

.search-input {
  margin-right: 10px;
}

/* to override default vuetify min-height */
.v-list--two-line .v-list-item {
  min-height: 30px;
  cursor: pointer;
}

.v-list-item:hover {
  background-color: #ebebeb;
}
</style>
