<template>
  <div id="homePage">
    <!-- <img alt="Vue logo" src="../assets/logo.png" /> -->
    <HelloWorld msg="Welcome to Your Vue.js App" />
    <header>
      <h1>todos</h1>
    </header>
    <section class="main-part">
      <section>
        <div class="input-edit-wrap">
          <input type="checkbox" class="input" id="check-all" :checked="check_all" @change="toggleCheckAll">
          <label for="check-all" class="label" :class="{'checked-all':check_all,'opacity1':showCheckAll}"></label>
          <input class="edit" type="text" v-model="edit" @keydown.enter="addTodo" name="" id=""
            placeholder="what needs to be done ?">
          <span class="del" v-if="showDel" @click="delInput"></span>
        </div>
      </section>
      <section v-if="task_list.length>0">
        <div class="input-edit-wrap input-edit-wrap2" v-for="item in show_list" :key="item.id">
          <input type="checkbox" class="input" :id="'item'+item.id" v-model="item.checkbox" @change="toggleItem(item)">
          <label :for="'item'+item.id" class="label" :class="{'checked-item':item.checkbox}"></label>
          <input class="edit" type="text" name="" id="" placeholder="what needs to be done ?" v-if="false">
          <div class="text">{{item.text}}</div>
          <span class="del" @click="delItem(item)"></span>
        </div>
      </section>
      <section>
        <div>
          <div class="show-task-wrap">
            <div>total: {{total}}</div>
            <div>unCompleted: {{unCompleted}}</div>
            <div>completed: {{completed}}</div>
          </div>
          <div class="show-btn-wrap">
            <div class="btn" @click="showAll">showAll</div>
            <div class="btn" @click="showUncomplete">Uncomplete</div>
            <div class="btn" @click="showCompleted">completed</div>
            <div class="btn" @click="clearCompleted">clear completed</div>
            <div class="btn" @click="clearUncompleted">clear uncompleted</div>
            <div class="btn" @click="clearAll">clear all</div>
          </div>

        </div>
      </section>

    </section>
  </div>
</template>

<script>
  // @ is an alias to /src
  import HelloWorld from "@/components/HelloWorld.vue";

  export default {
    name: "Home",
    components: {
      HelloWorld
    },
    data() {
      return {
        edit: '',
        check_all: false,
        task_list: [],
        total: 0,
        unCompleted: 0,
        completed: 0,
        show_list: [], //展示的任务列表
        show_type: 1, //展示类型 1：all 2:uncompleted 3: completed
      }
    },
    computed: {
      showDel() {
        return this.edit.length > 0
      },
      showCheckAll() {
        console.log('this.task_list',this.task_list.length);
        return this.task_list.length > 0 && (this.show_type == 1 || this.show_type == 2)
      },

    },

    watch: {
      task_list() {
        console.log('list发生变化');
        this.UpdateTaskCount();
      },
      show_type(){
        if(this.show_type==1){
          this.show_list=this.task_list;
        }else if(this.show_type==2){
          this.show_list=this.getUncompletedTask();
        }else if(this.show_type==3){
          this.show_list=this.getCompletedTask();
        }
      }
    },
    created() {

      if (this.show_type == 1) {
        this.show_list = this.task_list;
        console.log('aaa');
      } else if (this.show_type == 2) {
        console.log('bbb');
        this.show_list = this.getUncompletedTask();
      } else if (this.show_type == 3) {
        this.show_list = this.getCompletedTask();
      }
    },
    methods: {
      //更新任务类型的数量
      UpdateTaskCount() {
        console.log('更新任务类型的数量');
        this.total = this.task_list.length;
        this.unCompleted = this.getUncompletedTask().length;
        this.completed = this.getCompletedTask().length;
      },

      //获取未完成的任务
      getUncompletedTask() {
        return this.task_list.filter(item => {
          return !item.checkbox
        })
      },

      //获取已完成任务
      getCompletedTask() {
        return this.task_list.filter(item => {
          return item.checkbox
        })
      },

      //切换选择完成全部
      toggleCheckAll() {
        console.log('切换全部');
        this.check_all = !this.check_all;
        this.task_list = this.task_list.map(item => {
          item.checkbox = this.check_all;
          return item
        })
        this.UpdateTaskCount()
      },

      //添加todo
      addTodo() {
        if (this.edit.length > 0) {
          let item = {};
          item.id = new Date().getTime();
          item.text = this.edit;
          item.checkbox = false;
          this.task_list.push(item);
          this.edit = "";
        }
      },

      //全选完成
      completeAll() {
        this.task_list = this.task_list.map(item => {
          if (item.checkbox) {
            return item.checkbox = true;
          }
        })
      },

      //删除输入项
      delInput() {
        this.edit = '';
      },

      //切换当前项的完成状态
      toggleItem(todo) {
        console.log('切换当前项的完成状态', todo);
        this.check_all = this.task_list.every(item => {
          console.log('item', item);
          return item.checkbox;
        })
        this.UpdateTaskCount()
      },

      //删除当前项
      delItem(todo) {
        console.log(todo);
        this.task_list = this.task_list.filter(item => {
          return item.id != todo.id;
        })
      },

      //展示所有任务
      showAll() {
        this.show_list = this.task_list;
        this.show_type = 1;
      },

      //展示未完成的任务
      showUncomplete() {
        this.show_list = this.getUncompletedTask();
        this.show_type = 2;
      },

      //展示完成任务
      showCompleted() {
        this.show_list = this.getCompletedTask();
        this.show_type = 3;
      },

      //删除所有任务
      clearAll() {
        this.task_list = [];
        this.clearCurrentList()
        this.showAllType();
      },

      //删除未完成任务
      clearUncompleted() {
        this.task_list = this.task_list.filter(item => {
          return item.checkbox
        })
        this.clearCurrentList()
        this.showAllType();
      },

      //删除已完成任务
      clearCompleted() {
        this.task_list = this.task_list.filter(item => {
          return !item.checkbox
        })
        this.clearCurrentList()
        this.showAllType();
      },

      //清空当前展示项
      clearCurrentList() {
        this.show_list = [];
      },

      //展示全部类型
      showAllType(){
        this.show_type=1;
      }

    }
  };
</script>
<style lang="less" scoped>
  #homePage {}

  .main-part {
    width: 800px;
    margin: 0 auto;
    box-shadow: 2px 2px 5px #888888;
  }

  .input-edit-wrap {
    height: 60px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 20px;
    border-bottom: 1px solid #999;

    .input {
      // display: none;
    }

    .label {
      width: 60px;
      height: 60px;
      // border-radius: 50%;
      width: 40px;
      height: 40px;
      background: url('../assets/img/checkbox1.png') no-repeat center center;
      background-size: contain;
      cursor: pointer;
      opacity: 0;
    }

    .opacity1 {
      opacity: 1;
    }

    .checked-all {
      background-image: url('../assets/img/checkbox2.png');
    }

    .edit {
      flex: 1;
      height: 100%;
      border: 0 none;
      padding: 0 20px;
      outline: medium;
      font-size: 24px;
    }

    .del {
      display: block;
      width: 40px;
      height: 40px;
      background: url('../assets/img/del.png') no-repeat center center;
      background-size: contain;
      cursor: pointer;
    }
  }

  .input-edit-wrap2 {
    .text {
      flex: 1;
      text-align: left;
      padding: 0 20px;
      font-size: 24px;
    }

    .label {
      opacity: 1;
      background-image: url('../assets/img/radio1.png');
    }

    .checked-item {
      background-image: url('../assets/img/radio2.png');
    }
  }

  .show-task-wrap {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 60px;
    padding: 0 20px;
    border-bottom: 1px solid #999;

    .btn {
      cursor: pointer;
    }
  }

  .show-btn-wrap {
    height: 60px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 20px;

    .btn {
      cursor: pointer;
    }
  }
</style>