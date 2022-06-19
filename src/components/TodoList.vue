<template>
<div>
  <ul>
    <li v-for="(todo,index) in todos" :key="todo.id">
      <div class="checkbox-div d-flex">
        <div class="d-flex align-items-center flex-grow-1">
          <input
            type="checkbox"
            :checked="todo.completed"
            @change="toggleTodo(index)"
          />
          <label :class="{'todoName': todo.completed}">
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
</template>

<script>
export default {
  props:{
    todos:{
      type:Array,
    }
  },
  emits:['toggle-todo','deleteTodo'],
  setup(props,{emit}){
    const toggleTodo = (index) => {
      emit('toggle-todo',index);
    };
    const deleteBtn = (index) => {
      // console.log(index)
      emit('deleteTodo',index)
    };
    return {toggleTodo,deleteBtn};

  },
}
</script>

<style scoped>
.todoName {
  text-decoration: line-through;
  color: #888;
}
</style>