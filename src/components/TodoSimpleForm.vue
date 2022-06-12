<template>
    <form @submit.prevent="onsubmit">
      <div class="d-flex">
        <div class="flex-grow-1">
        <input
          class="form-control"
          type="text"
          v-model="todo"
          placeholder="리스트를 입력하세요."
        />
      </div>
        <div>
        <button
          class="btn btn-primary"
          type="submit"
        >
          add
        </button>
      </div>
      </div>
      <div>
        <span
          v-if="isError"
          style="color: red">
          입력해주세요.
        </span>
      </div>
    </form>
</template>

<script>
import { ref } from 'vue';
export default {
  setup(props, context){
    const todo = ref('');
    const isError = ref(false);
    const onsubmit = () => {
      if(todo.value === ''){
        isError.value = true;
      } else {
        context.emit ('add-todo',{
          id:Date.now(),
          subject:todo.value,
          completed:false,
        });
        isError.value = false;
        todo.value = '';
      }
    };
    return {
      todo,
      isError,
      onsubmit
    };
  }
}
</script>

<style scoped>

</style>