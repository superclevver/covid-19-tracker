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
      <div class="buttons">
        <div class="buttonGroup">
          <v-select
            class="style-chooser"
            v-model="regionSelected"
            label="name"
            :options="regions"
            @input="getReportsByRegion"
          ></v-select>
          <!-- <div class="button" @click="getReportsByRegion"><font-awesome-icon icon="search"/>Search</div> -->
        </div>
      </div>
      <div class="tables">
        <div class="titleAndTable">
          <div class="title">Regions</div>
          <div class="globalTableWrapper">
            <table>
              <thead>
                <tr>
                  <td>#</td>
                  <td>Region</td>
                  <td>Confirmed cases</td>
                  <td>Deaths</td>
                  <td>Recovered cases</td>
                  <td>Active cases</td>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="(region, index) in reportsByRegions"
                  :key="region.iso"
                >
                  <td>{{ index }}</td>
                  <td>{{ region.name }}</td>
                  <td v-if="region.confirmed">{{ region.confirmed }}</td>
                  <td v-else>-</td>
                  <td v-if="region.deaths">{{ region.deaths }}</td>
                  <td v-else>-</td>
                  <td v-if="region.recovered">{{ region.recovered }}</td>
                  <td v-else>-</td>
                  <td v-if="region.active">{{ region.active }}</td>
                  <td v-else>-</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <div class="titleAndTable">
          <div class="title">{{ regionSelected.name }}</div>
          <div class="globalTableWrapper">
            <table>
              <thead>
                <tr>
                  <td>#</td>
                  <td>Subregion</td>
                  <td>Confirmed cases</td>
                  <td>Deaths</td>
                  <td>Recovered cases</td>
                  <td>Active cases</td>
                </tr>
              </thead>
              <tbody>
                <tr 
                  v-for="(reportByRegion, index) in reportsByRegion"
                  :key="reportByRegion.region.iso + reportByRegion.region.name"
                >
                  <td>{{index}}</td>
                  <td v-if="reportByRegion.region.province">{{reportByRegion.region.province}}</td>
                  <td v-else>{{reportByRegion.region.name}}</td>
                  <td>{{reportByRegion.confirmed}}</td>
                  <td>{{reportByRegion.deaths}}</td>
                  <td>{{reportByRegion.recovered}}</td>
                  <td>{{reportByRegion.active}}</td>
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
import vSelect from "vue-select";
import "vue-select/dist/vue-select.css";

export default {
  name: "App",
  components: {
    "v-select": vSelect,
  },
  data() {
    return {
      globalData: {
        date: "Loading...",
        last_update: "Loading...",
        confirmed: "Loading...",
        confirmed_diff: "Loading...",
        deaths: "Loading...",
        deaths_diff: "Loading...",
        recovered: "Loading...",
        recovered_diff: "Loading...",
        active: "Loading...",
        active_diff: "Loading...",
        fatality_rate: "Loading...",
      },
      regions: [],
      reports: [],
      reportsByRegions: [],
      regionSelected: {
        iso: "",
        name: "Select a region",
      },
      reportsByRegion: [],
    };
  },
  async created() {
    this.getTotalData();
    await this.getRegions();
    await this.getReports();
    await this.getReportsByRegions();
  },
  methods: {
    getTotalData() {
      fetch("https://covid-api.com/api/reports/total")
        .then((response) => response.json())
        .then((json) => {
          this.globalData = json.data;
          // console.log(this.globalData);
        });
    },
    async getRegions() {
      return fetch("https://covid-api.com/api/regions")
        .then((response) => response.json())
        .then((json) => {
          this.regions = json.data;
          this.regions = this.regions.sort((a, b) =>
            a.name.localeCompare(b.name)
          );
          // console.log(this.regions);
        });
    },
    async getReports() {
      return fetch("https://covid-api.com/api/reports")
        .then((response) => response.json())
        .then((json) => {
          this.reports = json.data;
        });
    },
    async getReportsByRegions() {
      for (let index = 0; index < this.regions.length; index++) {
        const reportByRegion = {
          iso: "",
          name: "",
          confirmed: 0,
          deaths: 0,
          recovered: 0,
          active: 0,
        };
        const region = this.regions[index];
        reportByRegion.iso = region.iso;
        reportByRegion.name = region.name;

        for (let j = 0; j < this.reports.length; j++) {
          const report = this.reports[j];
          if (report.region.iso === region.iso) {
            reportByRegion.confirmed += report.confirmed;
            reportByRegion.deaths += report.deaths;
            reportByRegion.recovered += report.recovered;
            reportByRegion.active += report.active;
          }
        }
        this.reportsByRegions.push(reportByRegion);
      }
    },
    async getReportsByRegion() {
      if (this.regionSelected.name !== 'Select a region') {
        this.reportsByRegion = [];
        for (let index = 0; index < this.reports.length; index++) {
          const report = this.reports[index];
          if (this.regionSelected.iso === report.region.iso) {
            this.reportsByRegion.push(report);
          }
        }
      }
    }
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Fira+Code&family=IBM+Plex+Mono&family=Inconsolata&family=Roboto+Mono&family=Source+Code+Pro&display=swap");

:root {
  --bg-color: #262a33;
  --main-color: #43ffaf;
  --caret-color: #43ffaf;
  --sub-color: #526777;
  --text-color: #e5f7ef;
  --error-color: #ff5f5f;
  --error-extra-color: #d22a2a;
  --colorful-error-color: #ff5f5f;
  --colorful-error-extra-color: #d22a2a;
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

.centerTables {
  border-radius: 0.25rem;
  padding: 2rem;
  display: grid;
  gap: 2rem;
  grid-template-rows: 3rem auto;
  grid-template-areas:
    "title buttons"
    "tables tables";
  grid-template-columns: 1fr 1fr;
}

.centerTables .buttons {
  grid-area: buttons;
  display: -ms-grid;
  display: grid;
  gap: 1rem;
  grid-template-columns: 1fr 1fr;
  align-self: center;
}

.centerTables .buttons .buttonGroup {
  display: grid;
  gap: 1rem;
  grid-area: 1/2;
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
  font-size: 2rem;
  line-height: 2rem;
  margin-bottom: 0.5rem;
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

table tbody {
  color: var(--text-color);
}

table tbody tr:nth-child(odd) td {
  background: rgba(0, 0, 0, 0.1);
}

.style-chooser {
  height: 100%;
  width: 100%;
  border-radius: 0.25rem;
  background-color: rgba(0, 0, 0, 0.1);
}

.style-chooser .vs__search::placeholder {
  color: var(--sub-color);
  font-family: var(--font);
}

.style-chooser .vs__selected {
  color: var(--text-color);
}

.style-chooser .vs__dropdown-menu {
  border: none;
  background-color: var(--bg-color);
}

.style-chooser .vs__dropdown-menu li {
  color: var(--sub-color);
}

.style-chooser .v-list-item--link::before {
  background-color: red;
}

.style-chooser .vs__dropdown-menu .vs__dropdown-option--selected {
  background-color: rgba(0, 0, 0, 0.2);
  color: var(--text-color);
}

.style-chooser .vs__clear,
.style-chooser .vs__open-indicator {
  fill: var(--sub-color);
}

.style-chooser .vs__clear:hover,
.style-chooser .vs__open-indicator:hover {
  fill: var(--main-color);
}

.button {
  color: var(--text-color);
  cursor: pointer;
  transition: 0.25s;
  padding: 0.4rem;
  border-radius: 0.25rem;
  background: rgba(0, 0, 0, 0.3);
  text-align: center;
  -webkit-user-select: none;
  align-content: center;
  height: min-content;
  height: -moz-min-content;
  line-height: 1rem;
  transition: 0.65s ease-in-out;
}

.button svg {
  margin-right: 0.5rem;
}

.button:hover {
  background: var(--main-color);
  color: var(--bg-color);
}
</style>
