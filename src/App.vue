<template>
  <div class="navbar">
    <button class="filterBtn" v-if="!filter" @click="filter = true">
      <i class="fa-solid fa-filter"></i> Start Filter
    </button>
    <button class="filterBtn" v-else @click="stopFilter">
      <i class="fa-solid fa-filter"></i> Stop Filter
    </button>
  </div>
  <div class="container">
    <table>
      <thead>
        <FilterMenu @close="closeMenu" @startFilter="filterApply" ref="menu"> 
          <input class="searchBar" placeholder="Enter..." v-model="searchText" />
          <div class="distinctRecords">
            <div v-if="filteredResources.length != 0">
              <input type="checkbox" id="selectAll" @change="selectAll" v-model="allSelected" />
              <label for="selectAll">Select All</label>
            </div>
            <h6 v-else>{{"No matches found"}}</h6>
            <div v-for="(data,index) in filteredResources" :key="index">
              <input type="checkbox" :id="data" :value="data" v-model="selected" @click="select" />
              <label :for="data">{{data}}</label>
            </div>
          </div>
        </FilterMenu>
        <tr>
          <th data-column="id">id <i class="fa-solid fa-square-caret-down" v-if="filter" @click="[setPicked(),openMenu()]"></i></th>
          <th data-column="first_name">First Name <i class="fa-solid fa-square-caret-down" v-if="filter" @click="[setPicked(),openMenu()]"></i></th>
          <th data-column="last_name">Last Name <i class="fa-solid fa-square-caret-down" v-if="filter" @click="[setPicked(),openMenu()]"></i></th>
          <th data-column="gender">Gender <i class="fa-solid fa-square-caret-down" v-if="filter" @click="[setPicked(),openMenu()]"></i></th>
          <th data-column="phone">Phone <i class="fa-solid fa-square-caret-down" v-if="filter" @click="[setPicked(),openMenu()]"></i></th>
          <th data-column="country">Country <i class="fa-solid fa-square-caret-down" v-if="filter" @click="[setPicked(),openMenu()]"></i></th>
          <th data-column="ip_address">IP Address <i class="fa-solid fa-square-caret-down" v-if="filter" @click="[setPicked(),openMenu()]"></i></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data,index) in filterData()" :key="index">
          <td>{{ data.id }}</td>
          <td>{{ data.first_name }}</td>
          <td>{{ data.last_name }}</td>
          <td>{{ data.gender }}</td>
          <td>{{ data.phone }}</td>
          <td>{{ data.country }}</td>
          <td>{{ data.ip_address }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import Data from './assets/MOCK_DATA_1.json';
import FilterMenu from './components/FilterMenu.vue';
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
      menuStyle: {
        top: 0,
        left: 0,
        menuItemPicked: 'first_name',
        isOpen: false,
      },
      isApply: false,
    }
  },
  provide() {
    return {
      menuStyle: this.menuStyle,
    }
  },
  computed: {
    filteredResources () {
      let arr = this.Data;
      let value = this.picked;
      if(this.searchText){
        if(typeof this.Data[0][value] === "string")
          arr = this.Data.filter((item)=> item[value].toLowerCase().includes(this.searchText.toLowerCase()));
        else{
          arr =  this.Data.filter((item)=> item[value] === parseInt(this.searchText));
        }
      }
      arr = [...new Set(arr.map(item => item[value]))].sort();
      this.selected = arr;
      return arr;
    }, 
    selectAll() {
      this.selected = [];
      if (this.allSelected) {
        for (let key in this.Data) {
            var obj = this.Data[key];
            this.selected.push(obj[this.picked]);
        }
      }
    },
  },
  methods: {
     filterData() {
      this.copyData = this.copyData;
      if(this.filter && this.isApply){
        const result = this.copyData.filter((data) => this.selected.includes(data[this.picked]));
        this.menuStyle.isOpen = false;
        return result;
      }
      return this.copyData;
    },
    select(event) {
      this.allSelected = false;
      let value = event.target.value;
      if(this.selected.includes(value)){
          this.selected = this.selected.filter(item => item !== value)
      }
    },
    setPicked() {
        this.picked = event.target.parentElement.attributes["data-column"].nodeValue;
    },
    openMenu(){
      this.isApply = false;
      this.menuStyle.top = (event.layerY - 2) + "px";
      this.menuStyle.left = (event.layerX) + "px";
      this.menuStyle.menuItemPicked = this.picked;
      this.searchText = "";
      this.menuStyle.isOpen = !this.menuStyle.isOpen;
    },
    closeMenu(){
      this.menuStyle.isOpen = false;
      this.allSelected = true;
      this.selected = [...new Set(this.Data.map(item => item[this.picked]))].sort();
    },
    filterApply(){
      this.isApply = true;
    },
    stopFilter() {
      this.closeMenu();
      this.filter = false;
      this.filterData();
    }
  },
  components: {
    FilterMenu
  }
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
    height: 100%;
    width: 100%;
    overflow: auto;
}
table { 
	width: 70%; 
	border-collapse: collapse; 
	margin: 50px auto;
  margin-bottom: 8rem;
  overflow: auto;
}
tr:nth-of-type(odd) { 
	background: #eee; 
}
tr:nth-of-type(even) { 
	background: rgb(255, 255, 255); 
}
td, th { 
	padding: 10px; 
	border: 1px solid #ccc; 
	text-align: left; 
	font-size: 18px;
}
thead{
  position : relative;
}
th { 
	background: #3498db; 
	color: white; 
	font-weight: bold; 
  vertical-align: middle;
}
th i{
  float: right;
  cursor: pointer;
  vertical-align: middle;
}
.distinctRecords{
  background: rgb(249, 249, 249);
  border: 1px solid rgb(214, 214, 214);
  padding: 0 5px;
  height: 60%;
  overflow: auto;
}
.distinctRecords input {
  margin: 5px;
}
.searchBar{
  outline: none;
  padding: 0.3rem 0.5rem;
  margin: 10px 0;
  width: 100%;
}
</style>
