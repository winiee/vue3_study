<template>
  <div class="container mt-4">
    <h1 class="mb-4">TO-DO LIST</h1>
    <input
      class="form-control"
      type="text"
      v-model="searchText"
      placeholder="검색어를 입력하세요."
    />
    <hr>
    <todoSimpleForm @add-todo="addTodo"/>
    <div>{{error}}</div>
    <div class="mt-4">
      <p class="mb-0" v-if="!filterSearch.length">추가된 TODO가 없습니다.</p>
      <todoList :todos="filterSearch" @toggle-todo="toggleTodo" @delete-todo="deleteTodo"/>
    </div>
  </div>
</template>

<script>
import { ref,computed } from 'vue';
import TodoSimpleForm from "@/components/TodoSimpleForm";
import TodoList from "@/components/TodoList";
import axios from 'axios';
export default {
  components: {TodoList, TodoSimpleForm},
  setup(){
    const todos = ref([]);
    const error = ref('');
    const addTodo = async (val) => {
      error.value ='';
      // 데이터베이스 투두를 저장
      try {
        const res = await axios.post('http://localhost:3001/todos',{
          subject:val.subject,
          completed:val.completed,
        });
        if(res) { return todos.value.push(res.data)}
      } catch(err){
        console.log(err)
        error.value = "post error"
      }
      //   .then(
      //   res => {
      //     if(res) {
      //       todos.value.push(res.data)
      //       console.log(res)
      //     }
      //   }
      // ).catch(
      //   err => {
      //     console.log(err);
      //     error.value = 'post error 입니다.'
      //   }
      // );
      // console.log(val);

    };
    const deleteTodo = (index) => {
      console.log(index);
      todos.value.splice(index,1);
    };
    const toggleTodo = (index) => {
      console.log(index)
      todos.value[index].completed = !todos.value[index].completed;
    };
    const searchText = ref('');
    const filterSearch = computed(() => {
      if(searchText.value){
        return todos.value.filter(todo => {
          return todo.subject.includes(searchText.value);
        })
      } else{
        return todos.value;
      }
    });
    return {
      todos,
      addTodo,
      deleteTodo,
      toggleTodo,
      searchText,
      filterSearch,
      error
    };
  },

};
</script>

<style>
ul {
  list-style: none;
}
input {
  height: 40px;
  padding: 0 10px;
}
input + input {
  margin-left: 10px;
}
</style>