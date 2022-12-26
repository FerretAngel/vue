<template>
  <div class="root" :style="theme">
    <table class="table" id="table">
      <tr class="tableHead">
        <template
          v-for="(headItem, headIndex) in haveTableHead
            ? tableHead
            : tableHeadArray"
        >
          <th :key="headIndex">
            {{ headItem }}
          </th>
        </template>
      </tr>
      <tr
        :class="dataIndex % 2 == 0 ? '' : 'gray'"
        class="tableData"
        v-for="(dataItem, dataIndex) in tableDataArray"
        :key="dataIndex"
      >
        <template v-for="(headItem, headIndex) in tableHeadArray">
          <td
            v-if="
              headIndex <
              (haveButton ? tableHeadArray.length - 1 : tableHeadArray.length)
            "
            :key="headIndex"
          >
            {{ dataItem[headItem] }}
          </td>
        </template>
        <td class="fill" v-if="haveButton">
          <div :class="optionCLass">
            <div class="btn" v-if="haveEdit" @click="editItemData(dataItem)">
              编辑
            </div>
            <div
              class="btn danger"
              v-if="haveDelete"
              @click="deleteItemData(dataItem)"
            >
              删除
            </div>
          </div>
        </td>
      </tr>
    </table>
    <div class="tips" v-if="tableDataArray.length == 0">没有任何数据...</div>
  </div>
</template>

<script>
export default {
  name: "ferret-table",
  props: {
    tableData: {
      type: Array,
      required: true,
    },
    tableHead: {
      type: Array,
    },
    editItem: {
      type: Function,
    },
    deleteItem: {
      type: Function,
    },
    themeColor: {
      type: String,
      default: () => {
        return `rgb(135, 206, 235)`;
      },
    },
  },
  data() {
    return {
      tableDataArray: [],
      tableHeadArray: [],
      optionCss: ["option", "oneBtn", "twoBtn"],
    };
  },
  computed: {
    haveTableHead() {
      return this.tableHead != undefined;
    },
    haveEdit() {
      return this.editItem != undefined;
    },
    haveDelete() {
      return this.deleteItem != undefined;
    },
    optionCLass() {
      let index = 1;
      if (this.haveEdit && this.haveDelete) {
        index = 2;
      }
      return this.optionCss[index] + " " + this.optionCss[0];
    },
    haveButton() {
      return this.haveEdit || this.haveDelete;
    },
    theme() {
      let theme = this.themeColor.toString();
      let colorArr = theme.match(/\d{1,3}/g);
      if (colorArr.length < 3) {
        console.error("ferrtTable:主题色不合规");
        colorArr = [135, 206, 235];
      }
      //判断是否含有透明度
      return `
        --theme-color: rgb(${colorArr[0]}, ${colorArr[1]}, ${colorArr[2]});
        --theme-color-bg: rgba(${colorArr[0]}, ${colorArr[1]}, ${
        colorArr[2]
      }, 0.3);
        --text-color: rgb(${this.textColor(colorArr[0])},${this.textColor(
        colorArr[1]
      )},${this.textColor(colorArr[2])});
        --first-title-color: rgba(${this.titleColor(
          colorArr[0]
        )},${this.titleColor(colorArr[1])},${this.titleColor(
        colorArr[2]
      )}, 0.8);
        --second-title-color: rgba(${this.titleColor(
          colorArr[0]
        )},${this.titleColor(colorArr[1])},${this.titleColor(
        colorArr[2]
      )}, 0.6);
        --third--title-color: rgba(${this.titleColor(
          colorArr[0]
        )},${this.titleColor(colorArr[1])},${this.titleColor(
        colorArr[2]
      )}, 0.3);
        --denger-color: rgba(255, 0, 0, 0.6);
      `;
    },
  },
  methods: {
    //反色
    titleColor(color) {
      if (typeof color == "number") {
        return 255 - color;
      } else {
        return 255;
      }
    },
    //互补色
    textColor(color) {
      // if(color<170){
      return (255 - color) % 100;
      // }else{
      //   return ((255-color)%100+170)%255
      // }
    },
    //
    getTableHeadArray() {
      if (this.tableData.length == 0) {
        this.setTableData();
        return;
      }
      this.tableHeadArray = Object.keys(this.tableData[0]);
      if (this.editItem || this.deleteItem) {
        this.tableHeadArray.push("option");
      }
      this.setTableData();
    },
    setTableData() {
      this.tableDataArray = this.tableData;
    },
    checkTableData() {
      if (undefined == this.tableData) {
        console.error("ferretTable:请绑定表数据");
        return false;
      } else {
        return true;
      }
    },
    editItemData(item) {
      if (this.editItem) {
        this.editItem(item);
      }
    },
    deleteItemData(item) {
      if (this.deleteItem) {
        this.deleteItem(item);
      }
    },
  },
  watch: {
    tableData() {
      this.getTableHeadArray();
    },
  },
  mounted() {
    if (this.checkTableData()) {
      this.getTableHeadArray();
    }
  },
};
</script>
<style scoped>
.root {
  display: block;
  margin: 0 auto;
  font-size: 20px;
  /* --theme-color: rgb(135, 206, 235);
  --theme-color-bg: rgba(135, 207, 235, 0.3);
  --text-color: rgb(255, 255, 255);
  --first-title-color: rgba(0, 0, 0, 0.8);
  --second-title-color: rgba(0, 0, 0, 0.6);
  --third--title-color: rgba(0, 0, 0, 0.3);
  --denger-color: rgba(255, 0, 0, 0.6); */
}
.table {
  margin: 0 auto;
  border-spacing: 0;
  border-collapse: collapse;
  font-size: 1em;
  max-width: 100%;
  max-height: 100%;
  overflow: scroll;
}
.table > .tableData {
  transition: all 0.3s;
  position: relative;
}
.table > .tableData:after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;

  background-color: rgba(0, 0, 0, 0);
  transition: all 0.3s;
  z-index: -1;
}
.table > .tableData:hover::after {
  background-color: rgba(100, 100, 100, 0.1);
}
.table > .tableHead {
  position: sticky;
  font-size: 1em;
  top: 0;
  background-color: var(--theme-color);
}
.table > .tableHead > th {
  color: var(--first-title-color);
}
.table > .tableHead::after {
  content: "";
}
.gray {
  background-color: var(--theme-color-bg);
}
.table > .tableData > td {
  padding: 0.25em 0.5em;
  color: var(--text-color);
  /* color: var(--first-title-color); */
  font-size: 1em;
}
.option {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}
.oneBtn {
  min-width: 4.5em;
}
.twoBtn {
  min-width: 6.5em;
}
.option > .btn {
  font-size: 0.8em;
  font-weight: 550;
  letter-spacing: 0.1em;
  padding: 0.15em 0.25em;
  border: 0.1em solid var(--theme-color);
  border-radius: 0.1em;
  color: var(--theme-color);
  transition: all 0.3s;
  /* 更改鼠标为手 */
  cursor: pointer;
  /* 不可选中 */
  user-select: none;
}
.option > .btn:hover {
  background-color: var(--theme-color);
  color: var(--first-title-color);
}
.danger {
  color: var(--denger-color) !important;
  border-color: var(--denger-color) !important;
}
.danger:hover {
  background-color: var(--denger-color) !important;
  color: var(--first-title-color) !important;
}
.fill {
  padding: 0 !important;
}
.tips {
  margin: 0 auto;
  font-size: 0.5em;
  text-align: center;
  display: block;
  color: var(--text-color);
}
</style>