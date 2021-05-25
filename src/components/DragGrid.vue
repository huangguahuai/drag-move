 
  
<template>
  <div id="dragview" class="index-page" >
    <div class="result-box" ref="dragArea" >  
        <grid-item 
          :id="item.id" v-for="(item, index) of source" :key="index"
          :item="item"
          :isHasMult="isHasMult"
          :selectDragItem="selectDragItem"
          :selectedList="selectedList"
          :isSelected="getSelect(item)"
          @change-mul-select="changeMulStatus"
          @recovery-drag="recoveryDrag"
          @set-drag-item="setDragItem"
        ></grid-item> 
    </div>
  </div>
</template>

<script>
import DragSelect from 'dragselect' 
import GridItem from './components/GridItem.vue';
export default {
  components: { GridItem }, 
  props: {
    source: {
      type: Array,
      default: () => []
    },
    selectedList: {
      type: Array,
      default: () => []
    }  
  },
  data() {
    return {    
        isHasMult: false,
        selectDragItem: null,  
    };
  },  
  mounted () {
      this.dragSelect()
  },
  methods: {   
    dragSelect() { 
      this.$nextTick(() => {
        const items = document.querySelectorAll(".grid-item")
        if(this.ds == null) {
          this.ds = new DragSelect({
            area: this.$refs.dragArea,
            draggability: false,
            immediateDrag: false,
            multiSelectKeys: ["ctrlKey", "shiftKey", "metaKey"],
            callback:(el) => {
              const ids =[]
              if(el.length > 0) {
                for(let e of el) {
                  ids.push(e.id)
                }
              } 
              // this.changeSelect(ids)
              this.$emit('change-select', ids)
            }
          })
          this.ds.stop();
          this.ds.addSelectables(items, false);
          this.ds.start();
        }
      })
    },
    setDragItem(item) {
      this.selectDragItem = item
      this.ds.stop()
    },
    recoveryStyle() {
      if(this.isHasMult) {
        for(let item of this.selectedList) {
          let div = document.getElementById(item.id) 
          if(div) {
               div.style.opacity = 1
          }
        }
      } else { 
        let div = document.getElementById(this.selectDragItem.id)
        div.style.opacity = 1
      }
    },
    changeMulStatus(e) {
      this.isHasMult = e
    },
    recoveryDrag() {
      this.ds = null
      this.dragSelect()
    },
    getSelect(item) {    
      let index = this.selectedList.findIndex(t => t.id == item.id) 
      return index != -1
    },   
  },
   
};
</script>

<style lang="scss">
.index-page {
  width: 100%;
  height: 100%;
  position: relative; 
  transition: all 0.3s ease; 
  .result-box {
    width: 100%;
    height: 100%;  
    display: flex; 
    flex-wrap: wrap;
    transition: all 0.3s;  
  }
} 

.bigger {
  width: 100%;
  padding: 0 0 0 100px;
  .result-box {
    height: 600px;
  }
}
 
</style> 