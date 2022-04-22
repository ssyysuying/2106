<template>
    <div class=''>
      <input type="text" placeholder="请输入添加内容" v-model="title" @keydown.enter="add"/>
      <div class="top">
        <span v-for="(item,i) in tab" :key="item" :class="tabCur === i ?'active':''" @click="tabClick(i)">{{item}}</span>
      </div>
     <ul class="con">
       <div>
         <h2>正在看的{{tab[tabCur]}}</h2>
         <button>时间排序</button>
       </div>
       <li v-for="(item,i) in cate" :key="item.id" v-show="item.cid === tabCur">
        <input
          type="text"
          v-model="item.title"
          v-if="editIndex == i"
          @blur="editIndex = -1"
        />
        <span @dblclick="editIndex = i" v-else> {{ item.title }}</span>
         <span>{{item.time | time}}</span>
         <button @click="del(i)">删除</button>
       </li>
     </ul>
    </div>
</template>

<script>
export default {
  data() {
    return {
      tab: ["书籍", "电影", "综艺", "体育", "游戏"],
      tabCur: 0,
      title: "",
      editIndex: -1,
      cate: [
        { title: "三国演义", id: 1, cid: 0, time: new Date() },
        { title: "太极", id: 2, cid: 1, time: new Date() },
        { title: "王牌对王牌", id: 3, cid: 2, time: new Date() },
        { title: "王者荣耀", id: 4, cid: 3, time: new Date() },
        { title: "nba", id: 5, cid: 4, time: new Date() }
      ]
    };
  },
  mounted() {
    if (localStorage.getItem("list")) {
      this.cate = JSON.parse(localStorage.getItem("list"));
    }
  },
  methods: {
    tabClick(i) {
      this.tabCur = i;
    },
    // 添加
    add() {
      // 判空
      if (this.title === "") return false;
      // 判重
      let res = this.cate.filter(item => {
        return item.title == this.title;
      });
      if (res.length > 0) {
        alert("不能在重复添加");
        return false;
      }
      // 添加到数组
      this.cate.push({
        title: this.title,
        time: new Date(),
        id: this.cate.length + 1,
        cid: this.tabCur
      });
      localStorage.setItem("list", JSON.stringify(this.cate));
      this.title = "";
    },
    // 删除
    del(i) {
      this.cate.splice(i, 1);
      localStorage.setItem("list", JSON.stringify(this.cate));
    },
    // 修改内容
    edit(title) {
      if (this.title === "") return false;
      this.editIndex = -1;
    }
  },
  // 过滤时间格式
  filters: {
    time(val) {
      let time = new Date(val);
      let year = time.getFullYear();
      let mouth = time.getMonth() + 1;
      let day = time.getDay();
      let hours = time.getHours();
      let mins = time.getMinutes();
      let seconds = time.getSeconds();
      return `${year}-${mouth}-${day} ${hours}:${mins}:${seconds}`;
    }
  }
};
</script>

<style lang='scss'>
input {
  width: 100%;
  height: 50px;
  line-height: 50px;
  margin-bottom: 30px;
}
.top {
  display: flex;
  justify-content: space-between;
}
.active {
  color: #f00;
}
.con {
  div {
    display: flex;
    justify-content: space-between;
    margin-top: 20px;
  }
  li {
    display: flex;
    justify-content: space-between;
    margin-top: 20px;
    height: 35px;
    line-height: 35px;
    button {
      display: none;
    }
  }
  li:hover {
    background: olivedrab;
    button {
      display: inline-block;
    }
  }
}
</style>
