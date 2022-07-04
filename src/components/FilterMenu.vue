<template>
  <div class="menuSection" :style="{...menuPosition}" v-if="menuStyle.isOpen">
    <ul id="menuOptions">
        <li @click="sortData(false)">
          <i class="fa-solid fa-arrow-down-a-z icon1"></i>
          {{typeof Data[0][this.picked] == 'string' ? "Sort A to Z":"Sort smallest to largest"}}
          </li>
        <li @click="sortData(true)">
          <i class="fa-solid fa-arrow-down-z-a icon2"></i>
          {{typeof Data[0][this.picked] == 'string' ? "Sort Z to A":"Sort largest to smallest"}}
        </li>
        <li @click="openOptionsMenu">
          <span>{{typeof Data[0][this.picked] == 'string' ? "Text Filters":"Number Filters"}}</span>
          <i class="fa-solid fa-caret-up options-icon" :style="{transform: isOptions ? 'rotate(180deg)' : 'rotate(0deg)'}"></i>
        </li>
    </ul>
    <div class="optionSection" v-show="isOptions">
      <select id="filterOptions" v-model="selectOption" @change="selectedOptions">
        <option>None</option>
        <template v-if="typeof Data[0][this.picked] == 'string'">
            <option value="empty">Is empty</option>
            <option value="not_empty">Is not empty</option>
            <option value="contain">Text contains</option>
            <option value="not_contain">Text doesn't contains</option>
            <option value="starts">Text starts with</option>
            <option value="ends">Text ends with</option>
            <option value="text_equals">Text is exactly</option>
        </template>
        <template v-else>
            <option value="greater">Greater than</option>
            <option value="greater_equal">Greater than or equal to</option>
            <option value="lesser">Lesser than</option>
            <option value="lesser_equal">Lesser than or equal to</option>
            <option value="equals">Is equal to</option>
            <option value="not_equals">Is not equal to</option>
        </template>
      </select>
      <input id="filterOptions" placeholder="Value" v-model="filterText" v-show="showInput" />
    </div>
    <slot />
    <div class="btnSection">
        <button @click="startFilter">Apply</button>
        <button @click="close">Cancel</button>
    </div>
  </div>
</template>

<script>
import Data from '../assets/MOCK_DATA_1.json';
export default {
    name:'FilterMenu',
    data() {
      return {
        Data: Data,
        copyData: Data,
        searchText: "",
        filterText: "",
        picked: 'first_name',
        selected: [],
        selectOption: 'None',
        allSelected: true,
        menuPosition: {
          top: 0,
          left: 0,
        },
        isOptions: false,
        showInput: false,
      }
  },
  inject: ['menuStyle'],
  updated(){
    this.menuPosition.top = this.menuStyle.top,
    this.menuPosition.left =  this.menuStyle.left,
    this.picked = this.menuStyle.menuItemPicked
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
    sortData(dsc = false){
      this.Data = this.Data.sort(this.getSortOrder(this.picked));
      if(dsc){
        this.Data.reverse();
      }
      this.menuStyle.isOpen = false;
    },
    openOptionsMenu(){
      this.isOptions = !this.isOptions;
    },
    close() {
      this.closeFilter();
      this.$emit('close');
    },
    startFilter(){
      this.$emit('startFilter',{
        "option": this.selectOption,
        "filterText": this.filterText,
      });
      this.closeFilter();
    },
    closeFilter() {
      this.selectOption = 'None';
      this.filterText = "";
      this.showInput = false;
      this.isOptions = false;
    },
    selectedOptions(){
      let value = this.selectOption;
      if(value === "None" || value === "empty" || value === "not_empty"){
        this.showInput = false;
      }else{
        this.showInput = true;
      }
    }
  },
}
</script>

<style scoped>
.menuSection{
  /* height: 25rem; */
    min-width: 15rem;
    background: #fff;
    box-shadow: rgba(99, 99, 99, 0.2) 0px 2px 8px 0px;
    padding: 0.5rem 0;
    padding-left: 1.5rem;
    position: absolute;
}
#menuOptions{
    list-style: none;
    font-size: 14px;
}
#menuOptions li:last-child{
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-right: 12px;
  -webkit-user-select: none;
  -moz-user-select: none; 
  -ms-user-select: none;
  user-select: none;
}
#menuOptions li{
    padding: 0.2rem 0;
    letter-spacing: 1.2px;
    cursor: pointer;
}
#menuOptions li:hover{
  background: rgba(235, 235, 235, 0.547);
}
.icon1{
    position: absolute;
    top: 12px;
    left: 5px;
}
.icon2{
    position: absolute;
    top: 36px;
    left: 5px;
}

.btnSection{
    padding: 8px 0;
    float: right;
}
.btnSection > *{
    margin-right: 10px;
    border-radius: 3px;
    padding: 5px 18px;
    border: 2px solid transparent;
    cursor: pointer;
    background: #d9d9d9;
}
.btnSection > *:hover{
    border: 2px solid blue;
}
.optionSection{
  display: flex;
  flex-direction: column;
}
#filterOptions {
    width: 95%;
    border: 1px solid gray;
    border-radius: 5px;
    font-size: 15px;
    padding: 5px 15px;
    outline: none;
    margin-top: 10px;
}
.options-icon {
    transition: all 0.2s ease-in-out; 
}
</style>