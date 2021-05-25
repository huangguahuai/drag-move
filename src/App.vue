<template>
  <div id="app"> 
    <DragGrid  
      :source="source"
      :selectedList="selectedList"
      @drag-move="dragMove"
      @change-select="changeSelect" 
    />
  </div>
</template>

<script>
import DragGrid from './components/DragGrid'

export default {
  name: 'App',
  components: {
    DragGrid
  },
  data () {
    return {
      source: [],
      selectedList: [],
    }
  },
  methods: {
    dragMove(source, des) {
      console.log(source, des)
    },
    changeSelect(ids) {
      this.selectedList = []
      for(let id of ids) {
        let index = this.source.findIndex(t => t.id == id)
        if(index > -1) {
          this.selectedList.push(this.source[index])
        }
      }   
    }, 
  },
  created() { 
    for (let i = 50; i <= 300; i++) {
      let type = 0
      if(i % 2 == 0) { 
        this.source.push({
          id: i,
          name: `Folder ${i}`,
          type: 1
        }) 
       continue
      }
      this.source.push({
        id: i,
        name: `File ${i}`,
        type: type
      });
    }
  }, 
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50; 
}
</style>
