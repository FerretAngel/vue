<template>
  <div class="root" ref="root">
    <div
      v-for="(item, index) in menu"
      :key="index"
      @click="updateValue(index)"
      @mouseover="hoverUpdate(index)"
      @mouseleave="hoverLeave"
      :style="index==value? (selectedItemStyle):''"
    >
      {{ item }}
    </div>
    <div class="tipsLine" :style="tipsLineStyle"></div>
  </div>
</template>

<script>
export default {
  name: "ferret_navigation",
  data() {
    return {
      menuOffset: [],
      value:0,
      oldValue:0,
    };
  },
  props: {
    menu: {
      type: Array,
      required: true,
    },
    click:{
        type: Function
    },
    themeColor:{
        type:String,
        default:()=>"rgb(135, 206, 235)"
    }
  },
  mounted() {
    this.getChildOffset();
    //监听窗口变化
    window.onresize=()=>{this.getChildOffset()}
  },
  computed: {
    tipsLineStyle() {
      let dom = this.menuOffset[this.value];
      if (typeof(dom) == "undefined") {
        return "";
      } else {
        return (`left:${dom.left-5}px;\
        width:${dom.width+10}px;\
        background-color: ${this.themeColor};`).trim();
      }
    },
    selectedItemStyle(){
        return `color: ${this.themeColor};`
    }
  },
  methods: {
    //拿到各元素的位置信息
    getChildOffset() {
      let arr = this.$refs.root.children;
      this.menuOffset.length=0
      for (let i = 0; i < this.menu.length; i++) {
        let child = {
          left: arr[i].offsetLeft,
          width: arr[i].clientWidth,
        };
        this.menuOffset.push(child);
      }
    },
    updateValue(index){
       this.oldValue=this.value=index;
       if(this.click!=undefined){
            this.click(index)
       }
    },
    hoverUpdate(index){
        this.value=index;
        if(this.timmer!=undefined){
            clearTimeout(this.timmer)
        }
    },
    hoverLeave(){
        this.timmer=setTimeout(()=>{
            this.value=this.oldValue
            this.timmer=undefined
        },300)
    }
  },
};
</script>

<style scoped>
.root {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  position: relative;
  font-size: 20px;
}
.root>div{
    cursor: pointer;
    user-select: none;
    transition: all .3s;
}
.root > .tipsLine {
  position: absolute;
  height: 0.25em;
  border-radius: 1em;
  top: 1.7em;
}
</style>