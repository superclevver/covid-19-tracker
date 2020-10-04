<template>
  <div id="app">
    <div class="center_content">
      <div class="stats morestats">
        <div class="group testType">
          <div class="top">Global Data</div>
          <div class="bottom">{{ globalData.last_update }}</div>
        </div>

        <div class="group info">
          <div class="top">Confirmed cases</div>
          <div class="bottom">{{ globalData.confirmed }}</div>
        </div>

        <div class="group raw">
          <div class="top">Deaths</div>
          <div class="bottom">{{ globalData.deaths }}</div>
        </div>
        <div class="group key">
          <div class="top">Recovered cases</div>
          <div class="bottom">{{ globalData.recovered }}</div>
        </div>

        <div class="group flat consistency">
          <div class="top">Active cases</div>
          <div class="bottom">{{ globalData.active }}</div>
        </div>
      </div>
    </div>
    <div class="centerTables">
      <div class="tables">
        <div class="titleAndTable">
          <div class="title">Countries</div>
          <div class="globalTableWrapper">
            <table>
              <thead>
                <tr>
                  <td>#</td>
                  <td>Country</td>
                  <td>Confirmed cases</td>
                  <td>Deaths</td>
                  <td>Recovered cases</td>
                  <td>Active cases</td>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(country, index) in regions" :key="country.iso">
                  <td>{{ index }}</td>
                  <td>{{ country.name }}</td>
                  <td></td>
                  <td></td>
                  <td></td>
                  <td></td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <div class="titleAndTable">
          <div class="title">Country name</div>
          <div class="globalTableWrapper">
            <table>
              <thead>
                <tr>
                  <td>#</td>
                  <td>Province</td>
                  <td>Confirmed cases</td>
                  <td>Deaths</td>
                  <td>Recovered cases</td>
                  <td>Active cases</td>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td>0</td>
                  <td>Province name</td>
                  <td>000000</td>
                  <td>000000</td>
                  <td>000000</td>
                  <td>000000</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      globalData: {
        date: "loading",
        last_update: "loading",
        confirmed: "loading",
        confirmed_diff: "loading",
        deaths: "loading",
        deaths_diff: "loading",
        recovered: "loading",
        recovered_diff: "loading",
        active: "loading",
        active_diff: "loading",
        fatality_rate: "loading",
      },
      regions: [],
    };
  },
  async created() {
    this.getTotalData();
    await this.getRegions();
  },
  methods: {
    getTotalData: function () {
      fetch("https://covid-api.com/api/reports/total")
        .then((response) => response.json())
        .then((json) => {
          this.globalData = json.data;
          console.log(this.globalData);
        });
    },
    getRegions: async function () {
      fetch("https://covid-api.com/api/regions")
        .then((response) => response.json())
        .then((json) => {
          this.regions = json.data;
          console.log(this.regions);
        });
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Fira+Code&family=IBM+Plex+Mono&family=Inconsolata&family=Roboto+Mono&family=Source+Code+Pro&display=swap");

:root {
  --bg-color: #262727;
  --main-color: #f0d3c9;
  --caret-color: #f0d3c9;
  --sub-color: #665957;
  --text-color: #fff;
  --error-color: #bd4141;
  --error-extra-color: #883434;
  --colorful-error-color: #bd4141;
  --colorful-error-extra-color: #883434;
  --font: "Roboto Mono";
}

::-webkit-scrollbar {
  width: 7px;
}
::-webkit-scrollbar-track {
  background: transparent;
}
::-webkit-scrollbar-thumb {
  background: var(--sub-color);
  transition: 0.25s;
  border-radius: 2px !important;
}
::-webkit-scrollbar-thumb:hover {
  background: var(--main-color);
}

body {
  background-color: var(--bg-color);
  font-family: var(--font);
  color: var(--main-color);
}

#app {
}

.center_content {
  max-width: 1000px;
  min-width: 500px;
  margin: 0 auto;
  padding: 2rem;
}

.stats.morestats {
  display: grid;
  grid-auto-flow: column;
  grid-template-areas: none;
  align-items: flex-start;
  justify-content: space-between;
  column-gap: 2rem;
  grid-area: morestats;
}

.stats.morestats .group .top {
  color: var(--sub-color);
}
.stats.morestats .group .bottom {
  font-size: 2rem;
}

.tables {
  grid-area: tables;
  display: grid;
  gap: 1rem;
  grid-template-columns: 1fr 1fr;
  margin-bottom: 2rem;
  font-size: 0.8rem;
}

.tables .titleAndTable {
  display: grid;
}

.tables .titleAndTable .title {
  grid-area: 1/1;
}

.tables .globalTableWrapper {
  height: calc(100vh - 22rem);
  overflow-y: scroll;
  overflow-x: hidden;
}

table {
  width: 100%;
  border-spacing: 0;
}

table thead {
  color: var(--sub-color);
  font-size: 0.75rem;
}

table tbody tr:nth-child(odd) td {
  background: rgba(0, 0, 0, 0.1);
}
</style>
