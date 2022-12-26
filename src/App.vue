<template>
  <div id="app">
    <fnavigation
        :menu="tableTitle"
        :click="click"
    ></fnavigation> 
  </div>
</template>

<script>
// import ftable from "./components/ferret_table.vue";
import fnavigation from "./components/ferret_navigation.vue";
export default {
  name: "app",
  components: {
    // ftable,
    fnavigation,
  },
  data() {
    return {
      r:100,
      b:200,
      g:100,
      index:0,
      tableTitle: ["日期hdjkahjkdhk", "名字jkljslakdj", "地址asd", "操作"],
      // tableTitle:['','','','',''],
      tableData: [],
    };
  },
  computed:{
    themeColor(){
      return `rgb(${this.r},${this.b},${this.g})`
    }
  },
  methods: {
    click(index){
      this.index=index
    },
    deleteItem(item) {
      console.log("delete:",item);
      this.tableData = this.tableData.filter((it) => it != item);
    },
    editItem(item) {
      console.log("edit", item);
      item.name="111"
    },
  },
  mounted() {
    // this.axios.get("http://jsonplaceholder.typicode.com/posts").then(
    //   (res) => {
    //     this.tableData = res.data;
    //   },
    //   (err) => {
    //     console.log(err);
    //   }
    // );
    // let obj={
    //   string:"string",
    //   number:1,
    //   boolean:false,
    //   array:['1',2]
    // }
    // window.obj=obj
    // console.log(JSON.stringify(obj));
    for (let i = 0; i < 10; i++) {
      let obj = {
        date: "2016-05-03" + " " + i,
        name: "王小虎" + " " + i,
        address: "上海市普陀区金沙江路 1516 弄" + " " + i,
      };
      this.tableData.push(obj);
    }
  },
};
</script>

<style>
</style>
