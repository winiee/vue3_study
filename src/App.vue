<template>
  <div class="container mt-4">
    <h1 class="mb-4">TO-DO LIST</h1>
    <todoSimpleForm @add-todo="addTodo"/>
    <div class="mt-4">
      <p class="mb-0" v-if="!todos.length">추가된 TODO가 없습니다.</p>
      <ul>
        <li v-for="(todo,index) in todos" :key="todo.id">
          <div class="checkbox-div d-flex">
            <div class="d-flex align-items-center flex-grow-1">
              <input type="checkbox" v-model="todo.completed">
              <label :class="{todoName: todo.completed}">
                {{todo.subject}}
              </label>
            </div>
            <button
              type="button"
              class="btn btn-sm btn-danger"
              @click="deleteBtn(index)"
            >
              delete
            </button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import TodoSimpleForm from "@/components/TodoSimpleForm";
export default {
  components: {TodoSimpleForm},
  setup(){
    const todos = ref([]);
    const addTodo = (val) => {
      console.log(val);
      todos.value.push(val)
    };
    const deleteBtn = (index) => {
      console.log('delete',index);
      todos.value.splice(index,1);
    };
    // style은 바뀔 필요 없기 때문에 ref 사용하지 않아도 됨.
    return {
      todos,
      addTodo,
      deleteBtn
    };
  },

};
</script>

<style>
.todoName {
  text-decoration: line-through;
  color: gray;
}
ul {
  list-style: none;
}
.checkbox-div {
  border:1px solid #000;
  padding: 5px;
}
input {
  height: 40px;
  padding: 0 10px;
}
input + input {
  margin-left: 10px;
}
.btn {
  margin-left: 10px;
}
</style>