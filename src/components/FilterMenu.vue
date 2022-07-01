<template>
  <div class="menuSection" :style="{...menuPosition}" v-if="menuStyle.isOpen">
    <ul id="menuOptions">
        <li @click="sortData(false)"><i class="fa-solid fa-arrow-down-a-z icon1"></i> Sort A to Z</li>
        <li @click="sortData(true)"><i class="fa-solid fa-arrow-down-z-a icon2"></i> Sort Z to A</li>
        <li>
          <span>Text Filters</span>
          <i class="fa-solid fa-caret-right"></i>
        </li>
    </ul>
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
        picked: 'first_name',
        selected: [],
        allSelected: true,
        menuPosition: {
          top: 0,
          left: 0,
        },
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
    close() {
      this.$emit('close');
    },
    startFilter(){
      this.$emit('startFilter');
    }
  },
}
</script>

<style scoped>
.menuSection{
    min-width: 15rem;
    background: #fff;
    height: 25rem;
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
</style>