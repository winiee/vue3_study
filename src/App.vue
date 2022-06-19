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
    <todoSimpleForm
      @add-todo="addTodo"
    />
    <div>{{error}}</div>
    <div class="mt-4">
      <p class="mb-0" v-if="!filterSearch.length">추가된 TODO가 없습니다.</p>
      <todoList
        :todos="filterSearch"
        @toggle-todo="toggleTodo"
        @delete-todo="deleteTodo"
      />
      <hr />
      <nav aria-label="Page navigation example">
        <ul class="pagination d-flex justify-content-center">
          <li class="page-item" v-if="currentPage !== 1 "><a class="page-link" @click="getTodos(currentPage - 1)" style="cursor:pointer">Previous</a></li>
            <li
              v-for="(page,index) in numberOfPages"
              :key="index"
              class="page-item"
              :class="currentPage === page ? 'active' : ''"
            >
              <a class="page-link" @click="getTodos(page)" style="cursor: pointer">{{page}}</a>
            </li>
          <li class="page-item" v-if="currentPage !==numberOfPages "><a class="page-link" @click="getTodos(currentPage + 1)" style="cursor:pointer">Next</a></li>
        </ul>
      </nav>
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
    const numberOfTodos= ref(0);
    const limit = ref(10);
    const currentPage = ref(1)
    const numberOfPages = computed(() => {
      return Math.ceil(numberOfTodos.value/limit.value)
    })
    const getTodos = async (page = currentPage.value) => {
      // 위에서 page의 default 값이 currentPage.value가 됨 즉 1
      currentPage.value = page; // 현재 페이지 업데이트
      error.value='';
      try{
        const res = await axios.get(`http://localhost:3000/todos?_page=${page}&_limit=${limit.value}`)
        numberOfTodos.value = res.headers['x-total-count'];
        console.log('get res',numberOfTodos.value); // -가 있으면 .으로 접근하는것이 아닌 이렇게 접근 해야함 ['']
        todos.value = res.data;
      }catch(err) {
        console.log(err);
        error.value = 'get error'
      }
    }
    getTodos();
    const addTodo =  async (todo) => {
      error.value =''; // 처음 POST 실핼할 때는 ''으로 초기화
      // console.log(todo.subject);
      // 데이터베이스에 todo를 저장
      try {
       const res = await axios.post('http://localhost:3000/todos',{
            subject:todo.subject,
            completed:todo.completed
        })
        console.log(res);
       todos.value.push(res.data)
      }catch(err) {
        console.log(err)
        error.value = 'post error 입니다';
      }
      // await axios.post('http://localhost:3000/todos',{
      //   // id는 자동으로 생성
      //   subject:todo.subject,
      //   completed:todo.completed
      // }).then(res => {
      //   console.log(res);
      //   todos.value.push(res.data);
      // }).catch( err => {
      //   console.log(err)
      //   error.value = 'error입니다';
      // });
    };
    const deleteTodo = async (index) => {
      error.value = '';
      console.log(index);
      const id = todos.value[index].id;
      console.log('id',id)
      try{
        const res = await axios.delete(`http://localhost:3000/todos/${id}`)
        todos.value.splice(index,1);
        console.log(res);
      }catch(err){
        console.log(err)
        error.value = 'delete error'
      }

    };
    const toggleTodo = async (index) => {
      error.value = '';
      console.log(index)
      const id = todos.value[index].id
      try{
        todos.value[index].completed = !todos.value[index].completed;
        const res = await axios.patch(`http://localhost:3000/todos/${id}`,{
          completed:todos.value[index].completed
        })
        console.log(res);

      }catch (err) {
        console.log(err);
        error.value = "toggle patch error";
      }
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
      error,
      getTodos,
      numberOfTodos,
      limit,
      currentPage,
      numberOfPages
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