<template>
  <div class="navbar">
    <button class="filterBtn" v-if="!filter" @click="filter = true">Start Filter</button>
    <button class="filterBtn" v-else @click="filter = false">Stop Filter</button>
  </div>
  <div class="propertiesSection" v-show="filter">
    <p>Please select properties:</p>
    <div class="filterBar">
      <div>
        <input type="radio" id="ID" name="properties" value="id" v-model="picked">
        <label for="ID">ID</label>
      </div>
      <div>
        <input type="radio" id="firstName" name="properties" value="first_name" v-model="picked">
        <label for="firstName">First name</label>
      </div>
      <div>
        <input type="radio" id="LastName" name="properties" value="last_name" v-model="picked">
        <label for="LastName">Last name</label>
      </div>
      <div>
        <input type="radio" id="email" name="properties" value="email" v-model="picked">
        <label for="email">Email</label>
      </div>
      <div>
        <input type="radio" id="phone" name="properties" value="phone" v-model="picked">
        <label for="phone">Phone</label>
      </div>
      <div>
        <input type="radio" id="gender" name="properties" value="gender" v-model="picked">
        <label for="gender">Gender</label>
      </div>
      <div>
        <input type="radio" id="ipAddress" name="properties" value="ip_address" v-model="picked">
        <label for="ipAddress">IPAddress</label>
      </div>
      <button class="filterBtn" @click="sortData(false)">Sort A to Z</button>
      <button class="filterBtn" @click="sortData(true)">Sort Z to A</button>
    </div>
  </div>
    <h1 :style="{textAlign: 'center'}">Total length of Data: {{Data.length}}</h1>
  <div class="container">
    <div class="distinctRecords">
        <input type="text" class="searchBar" placeholder="Enter..." v-model="searchText"  />
        <div>
          <input type="checkbox" id="selectAll" @click="selectAll" v-model="allSelected">
          <label for="selectAll">Select All</label>
        </div>
        <div v-for="(data,index) in filteredResources" :key="index">
          <input type="checkbox" :id="data" :value="data" v-model="selected" @click="select" />
          <label :for="data">{{data}}</label>
        </div>
        <button class="filterBtn" @click="filterData">Apply</button>
        <!-- Cancel button to close the panel -->
    </div>
    <div class="grid">
        <!-- <v-grid theme="compact" :source="rows" :columns="columns"/> -->
      <h1 v-if="Data.length === 0">{{"No results found"}}</h1>
      <template v-for="(data,index) in filterData" :key="index">
        <div class="card">
          <h4>Id: {{ data.id }}</h4>
          <h2>Name: {{ data.first_name }} {{ data.last_name }}</h2>
          <p>Email: {{ data.email }}</p>
          <p>Phone: {{ data.phone }}</p>
          <p>Gender: {{ data.gender }}</p>
          <p>IP Address: {{ data.ip_address }}</p>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
// import VGrid from "@revolist/vue3-datagrid";
import Data from './assets/MOCK_DATA.json';
export default {
  name: "App",
  data() {
    return {
      Data: Data,
      copyData: Data,
      searchText: "",
      picked: 'first_name',
      filter: false,
      selected: [],
      allSelected: true,
    }
  },
  mounted() {
    this.distinctData();
  },
  computed: {
    filteredResources () {
      if(this.searchText){
        return this.copyData.filter((item)=> item[this.picked].toLowerCase().includes(this.searchText.toLowerCase()))
                            .map((item) => item[this.picked]);
      }else{
        return this.copyData.map((item) => item[this.picked]);
      }
    }, 
    filterData(){
      return this.Data.filter((data) => this.selected.includes(data[this.picked]));
    },
    selectAll() {
      this.selected = [];
      if (!this.allSelected) {
        for (let key in this.copyData) {
            var obj = this.copyData[key];
            this.selected.push(obj[this.picked]);
        }
      }
    },
  },
  methods: {
    getSortOrder(prop) {    
      return function(a, b) {    
          if (a[prop] > b[prop]) {    
              return 1;    
          } else if (a[prop] < b[prop]) {    
              return -1;    
          }    
          return 0;    
      }    
    },
    sortData(dsc){
      this.Data = this.Data.sort(this.getSortOrder(this.picked));
      if(dsc){
        this.Data.reverse();
      }
    },
    distinctData() {
      this.selected = [...new Set(this.copyData.map(item => item[this.picked]))];
    },
    select() {
      this.allSelected = false;
    },
  }
  // data() {
  //   return {
  //     columns: [
  //       {
  //         name: "Birth",
  //         prop: "birthdate",
  //         columnType: "date",
  //         size: 150,
  //       },
  //       {
  //         prop: "name",
  //         name: "First",
  //       },
  //       {
  //         prop: "details",
  //         name: "Second",
  //       },
  //     ],
  //     rows: [
  //       {
  //         birthdate: "2022-08-24",
  //         name: "1",
  //         details: "Item 1",
  //       },
  //       {
  //         birthdate: "2022-08-24",
  //         name: "2",
  //         details: "Item 2",
  //       },
  //     ],
  //   };
  // },
  // components: {
  //   VGrid,
  // },
};
</script>

<style>
*,*::before,*::after,html{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background: #485460;
  height: 100vh;
  overflow: hidden;
}
.navbar{
  width: 100%;
  background: #282d31;
  padding: 20px;
  display: flex;
  justify-content: center;
}
.filterBtn{
  padding: 10px 20px;
  border-radius: 12px;
  border: none;
  color:antiquewhite;
  background: #5014B8;
  font-weight: 600;
  cursor: pointer;
  box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
}
.filterBtn:hover{
  opacity: 0.9;
box-shadow: rgba(0, 0, 0, 0.16) 0px 1px 4px;
}

.container{
    margin-top: 2.5rem;
    height: 100%;
    width: 100%;
    display: flex;
    justify-content: center;
    column-gap: 6rem;
}
.grid{
    height: 70%;
    padding: 10px;
    background: #fff;
    width: 40%;
    overflow: auto;
}
.card{
  background: rgb(174, 174, 174);
  display: flex;
  flex-direction: column;
  padding: 10px;
  margin: 0.5rem 0;
  text-align: center;
}
.searchBar{
  outline: none;
  padding: 0 0.5rem;
}
.propertiesSection{
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1rem;
}
.filterBar{
  margin-left: 20px;
  display: flex;
  align-items: center;
}
.filterBar > *{
  margin: 0 10px;
}
.distinctRecords{
  background: azure;
  height: 70%;
  overflow: auto;
}
.distinctRecords input {
  margin: 5px;
}
.btnSection{
  display: flex;
  padding: 5px;
}
.btnSection p{
  text-decoration: underline;
  font-size: 0.8rem;
  margin-left: 5px;
  cursor: pointer;
}
.btnSection p:hover{
  color: #5014B8;
}
</style>
