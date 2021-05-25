# drag-move
vue file 文件或文件夹拖拽移动效果

### demo
``` vue
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
```

## Attributes
| 参数           | 描述              | 类型           | 默认值              | 是否必填   |
| ------------- |------------------- |-------------- |------ |
| source | 源数据 | Array | [] |是 |
| selectedList| 选中的文件数组 |  Array  | [] |是 | 

## Events
| 事件名         | 描述              | 参数           | 描述      |
| ------------- |------------------- |-------------- |-----------|
| drag-move     | 当鼠标drop时，执行的移动方法  |  source（Array），des(Object)  | 被移动的文件集合，接受移动的目标对象|
| change-select | 更改选中文件集合  | ids （Array）|  选中的文件id的集合|
