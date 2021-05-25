<template>
    <div :class="['grid-item ',{'selected': isSelected}]" draggable="true"  @dragstart="dragStart($event, item)" @drop="drop($event, item)" @dragover.exact="dragover($event, item)" @dragleave.exact="dragLeave($event, item)" @dragend="dragend">
        <img v-if="item.type == 0" class="image" src="../../assets/image.png" >
        <img v-else class="image" src="../../assets/folder.png">
        <div class="title">{{item.name}}</div>
    </div>
</template>
<script>
export default {
    props: {
        item: {
            type: Object,
            default: null
        },
        isSelected: {
            type: Boolean,
            default: true
        },
        isHasMult: {
            type: Boolean,
            default: false
        },
        selectDragItem: {
            type: Object,
            default: null
        },
        selectedList: {
            type: Array,
            default: () => []
        }
    }, 
    data () {
        return {
            cloneDiv: null
        }
    },
    methods: {
        setCloneDiv(isMul) { 
            this.cloneDiv.className += ' dragStyle' 
            if (isMul && this.selectedList.length > 1) {
                const countDiv = document.createElement('div')
                countDiv.style.position = 'absolute'
                countDiv.style.top = '-5px'
                countDiv.style.right = '-10px'
                countDiv.style.width = '20px'
                countDiv.style.height = '20px'
                countDiv.style.lineHeight = '20px'
                countDiv.style.textAlign = 'center'
                countDiv.style.color = '#fff'
                countDiv.style.fontSize = '13px'
                countDiv.style.background = 'red'
                countDiv.style.borderRadius = '50%'
                countDiv.innerText = this.selectedList.length
                this.cloneDiv.appendChild(countDiv)
            } 
            document.body.appendChild(this.cloneDiv)
        }, 
        dragStart(e, item) {    
            this.$emit('set-drag-item', item)
            let div = document.getElementById(item.id) 
            this.cloneDiv = div.cloneNode(true) 
            e.dataTransfer.effectAllowed = 'move' 
            let index = this.selectedList.findIndex(t => t.id == item.id) 
            if(index == -1) {
                this.$emit('change-mul-select', false)
                this.setCloneDiv(false)
                e.dataTransfer.setDragImage(this.cloneDiv, 10, 10)  
                div.style.opacity = 0.4
            } else {
                this.$emit('change-mul-select', true)
                this.setCloneDiv(true)
                e.dataTransfer.setDragImage(this.cloneDiv, 10, 10)  
                for(let item of this.selectedList) {
                    let div = document.getElementById(item.id)
                    div.style.opacity = 0.4
                }
            }  
        },
        dragover(e, item) {
            e.preventDefault()
            e.stopPropagation() 
            if(item.type == 1) {
                if(this.isHasMult) {
                let index = this.selectedList.findIndex(t => t.id == item.id)
                if(index != -1) return
                } else {
                if(item.id == this.selectDragItem.id) return
                } 
                let div = document.getElementById(item.id)
                div.classList.add('drop-over')
            }
        },
        dragLeave(e, item) {
        e.preventDefault()
        e.stopPropagation()
        if(item.type == 1) {
            let div = document.getElementById(item.id)
            div.classList.remove('drop-over')
        }
        },
        drop(e, item) {
            e.preventDefault()
            if(item.type == 1) {
                let div = document.getElementById(item.id)
                div.classList.remove('drop-over')
            } 
            let sourceList = []
            if(this.isHasMult) {
                sourceList = this.selectedList.concat()
            } else {
                sourceList.push(this.selectDragItem)
            }
            let index = sourceList.findIndex(t => t.id == item.id)
            if(index != -1) return
            // 执行拖拽移动
            this.$emit('drag-move', sourceList, item)
        },
        dragend() { 
            this.$emit('recovery-drag') 
            this.recoveryStyle()
            if(this.cloneDiv) {
                document.body.removeChild(this.cloneDiv)
                this.cloneDiv = null
            }
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
    }
}
</script>
<style lang="scss">
.grid-item {
    user-select: none;
    width: 130px;
    height: 120px;  
    margin:  10px 15px;
    border-radius: 5px;
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center; 
    .image {
    width: 80px;
    height: 80px;
    margin-top: 10px;
    }
    .title {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    }
}
.dragStyle {
    width: 130px;
    height: 120px;
    background: #fff !important;
    color: #000;
    border-radius: 10px;
    position: absolute;
    top: -500px;
    opacity: 1 !important; 
    .dragCount {
        position: absolute ;
        top:0;
        right: 0;
        width: 20px;
        height: 20px;
        line-height: 20px;
        text-align: center;
        color: #fff;
        font-size: 13px;
        background: red;
        border-radius: 50%;
    }
}
.drop-over {
  background: #88bddb !important;
  border-radius: 10px;
}
.selected {
    background: #f1f1f1; 
    border-radius: 10px;
}
</style>