<script setup>
import { ref, watch, onMounted, computed } from 'vue';

  const todos = ref([]);  //할일들을 모아놓을 배열 객체
  const name = ref('');
  const input_content = ref(''); 
  const input_category = ref(null);


  //할일 함수-입력값을 받아옴
  const addTodo = () => {
    if( input_content.value.trim() === '' || 
    input_category.value === null){
      return
    }
    console.log('함수 실행!!!')
    todos.value.push({
      content : input_content.value,
      category  :input_category.value,
      createAt: new Date().getTime(),  
      done:false,  
    })
    //입력 후 초기화
    input_content.value = '';
    input_category.value = null;
  }

  //삭제 함수
  const removeTodo = (todo) => {
    todos.value = todos.value.filter((item)=> item !== todo )
  }

  // todos배열을 시간 순서대로 정리 -> 새로운 todo가 위에 보이게
  const todos_asc = computed(()=> todos.value.sort((a,b)=>{
    return b.createAt - a.createAt
  }))


  // watch-할일 입력된것을 인지 
  watch(todos,(newVal)=>{
    console.log(todos) 
    localStorage.setItem('todos', JSON.stringify(newVal))
  },{deep:true});  // deep - 프로퍼티나, 하위 뎁스도 감시

  // watch-이름 입력된것을 인지해서 localStorage에 등록
  watch( name, (newVal)=>{ 
    //console.log(newVal) 
    localStorage.setItem('name', newVal)
  } )

  //새로 열었을때(새로 마운트되었을때) 로컬스토리지에서 불러옴(없을때는 비워놈)
  onMounted(()=>{
    //console.log('마운트 되었습니다')
    name.value = localStorage.getItem('name') || ''; 
    todos.value = JSON.parse(localStorage.getItem('todos')) || []; 
  });
    
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h1 class="title"> 
        💦💦💦💦💦
        <input 
          type="text" 
          placeholder="name here"
          v-model.lazy = "name"
          >
      </h1>
    </section>

    <!--  todo 입력 -->
    <section class="create-todo">
      <h2>CREATE A TODO</h2> 
      <form id="new-todo-form" v-on:submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input 
          type="text" 
          placeholder="할일을 입력해 주세요" 
          v-model.lazy="input_content"
          >
        <div class="options">
          <label>
            <input 
              type="radio" 
              name="category" 
              value="business" 
              v-model="input_category"
              >
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input 
              type="radio" 
              name="category" 
              value="personal" 
              v-model="input_category"
              >
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        
        <input type="submit" value="Add TODO">
      </form> 
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :class="`todo-item ${ todo.done && 'done' }`">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>        
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="del" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
    
  </main>
</template>

