<template>
  <div>
    <h1>ToDoリスト</h1>
    <label for="all">
      <input
        type="radio"
        id="all"
        name="sort-radio"
        value="all"
        @click="sortStatus('block','block')"
        checked
      />すべて
    </label>
    <label for="working">
      <input
        type="radio"
        id="working"
        name="sort-radio"
        value="working"
        @click="sortStatus('none','block')"
      />作業中
    </label>
    <label for="done">
      <input
        type="radio"
        id="done"
        name="sort-radio"
        value="done"
        @click="sortStatus('block','none')"
      />完了
    </label>
    <p style="font-weight: bold">
      ID
      <span class="status-comment">コメント</span>
      <span class="status-text" :style="{ paddingLeft: statusPadding }"
        >状態</span
      >
    </p>
    <ul id="tasklist" style="list-style: none; padding: 0">
      <li class="task-item" :key="task.id" v-for="(task, index) in tasks">
        <span class="task-index">{{ index }}</span>
        <span class="task-text" :style="{ width: taskWidth }">{{
          task.taskName
        }}</span>
        <button class="work-btn work" @click="changeStatus">
          作業中
        </button>
        <button class="deletebtn" @click="deleteTask($event);checkLength()">削除</button>
      </li>
    </ul>
    <h2>新規タスクの追加</h2>
    <input
      id="task-textbox"
      type="text"
      v-model="taskName"
    />
    <button id="add-btn" @click="addTask();checkLength()">追加</button>
  </div>
</template>

<script>
//vueインスタンス
export default {
  data() {
    return {
      status: '',
      taskName: '',
      tasks: [],
      maxLength: '',
      statusPadding: '37px',
      taskWidth: '100px',
    };
  },
  methods: {
    //追加ボタンでtaskを追加
    addTask() {
      const taskObj = { id: this.tasks.length, taskName: this.taskName };
      this.tasks.push(taskObj);
      this.taskName = ''; //テキストボックスを空白にす
    },
    //作業中ボタンクリックで状態を変更
    changeStatus(e) {
      if (e.target.textContent !== '完了') {
        e.target.textContent = '完了';
        e.target.classList.add('done');
        e.target.classList.remove('work');
      } else {
        e.target.textContent = '作業中';
        e.target.classList.add('work');
        e.target.classList.remove('done');
      }
    },
    //削除ボタンクリックでタスクオブジェクト削除
    deleteTask(e) {
      const deleteEl = e.target.parentNode; //クリックしたボタンの親要素取得
      const taskItemLists = Array.from(document.querySelectorAll('.task-item')); //htmlコレクションを配列化
      const deleteIndex = taskItemLists.indexOf(deleteEl); //クリックされた親要素（li）が何番目か検出
      this.tasks.splice(deleteIndex, 1); //クリックされたオブジェクト削除
      //idが重複しないよう削除した後にtasksのobjのそれぞれのid値を変更する。
      for (let i = 0; i < this.tasks.length; i++) {
        this.tasks[i].id = i;
      }
    },
    //taskNameの文字数に応じて幅を可変する
    checkLength(){
      //tasksのtaskName.lengthの最大値を取得
      this.maxLength = Math.max.apply(
        null,
        this.tasks.map(function (o) {
          return o.taskName.length;
        })
      );
      //maxLengthが５文字以上ならliのtask-itemの幅を文字数に応じて増やす
      if (this.maxLength * 50 > 200) {
        this.taskWidth = (this.maxLength - 4) * 20 + 100 + 'px';
        //状態textも同じように幅を増やす
        this.statusPadding = (this.maxLength - 4) * 20 + 37 + 'px';
      } else {
        //5文字未満なら初期値に戻す
        this.taskWidth = '100px';
        this.statusPadding = '37px';
      }
    },
    sortStatus(texta,textb) {
      const doneLists = Array.from(document.getElementsByClassName('done'));
      const workLists = Array.from(document.getElementsByClassName('work'));
      for(let i = 0;i< doneLists.length;i++){
        doneLists[i].parentNode.style.display = texta
      }
      for(let i = 0;i< workLists.length;i++){
        workLists[i].parentNode.style.display = textb
      }
    }
  },
};
</script>

<style scoped>
.work-btn {
  margin-right: 10px;
  width: 60px;
}
.task-index {
  display: inline-block;
  width: 20px;
  margin: 0;
}
.task-item {
  margin: 0;
}
.task-index {
  margin-right: 8px;
}
.task-text {
  display: inline-block;
}
.status-comment {
  padding-left: 5px;
}
#add-btn {
  margin-left: 8px;
}
</style>>
