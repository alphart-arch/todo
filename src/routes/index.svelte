<script context="module" lang="ts">
  import type { Load } from "@sveltejs/kit";
  import { enhance } from "$lib/actions/form";

  export const load: Load = async ({ fetch }) => {
    const res = await fetch("/todos.json");

    if (res.ok) {
      const todos = await res.json();
      return {
        props: { todos }
      }
    }

    const { message } = await res.json();
    return {
      error: new Error(message)
    }
  };
</script>

<script lang="ts">
  import TodoItem from "$lib/todo-item.svelte";
  
  export let todos: Todo[];
  //const date=new Date().toLocaleString();

  const title = "TRIAL-QT-TODO-LIST";
  let Show="Show Done";
let yes=false;
  const processNewTodoResult = async (res: Response, form: HTMLFormElement) => {
    const newTodo = await res.json();
    //alert('CAlled');
    todos = [...todos, newTodo, newTodo];

    form.reset();
  };

  const processUpdatedTodoResult = async (res: Response) => {
    const updatedTodo = await res.json();
    todos = todos.map(t => {
      if (t.uid === updatedTodo.uid) return updatedTodo;
      return t;
    })
  };

  function callAlert(msg) {
alert(msg);
  }
</script>

<style>
  .todos {
    width: 100%;
    max-width: 42rem;
    margin: 4rem auto 0 auto;
  }
  /*.date{
    margin: 0 0 0.5rem 0;
    font-size: 28px;
    width: 100%;
    padding: 0.5em 1em 0.3em 1em;
    box-sizing: border-box;
		background: rgba(255, 255, 255, 0.05);
		border-radius: 8px;
		text-align: center;
  }
  */

  .new {
    margin: 0 0 0.5rem 0;
  }

  .new input {
    font-size: 28px;
    width: 100%;
    padding: 0.5em 1em 0.3em 1em;
    box-sizing: border-box;
		background: rgba(255, 255, 255, 0.05);
		border-radius: 8px;
		text-align: center;
  }

  .todos :global(input) {
    border: 1px solid transparent;
  }

  .todos :global(input:focus-visible) {
    box-shadow: inset 1px 1px 6px rgba(0, 0, 0, 0.1);
    border: 1px solid #ff3e00 !important;
    outline: none;
  }
</style>

<svelte:head>
  <title>{title}</title>
</svelte:head>
<div class="todos">
  <h1>{title}</h1>
  <!--<input type="text" name="date" aria-label="Present date" class="date" value={date}/>-->

    <form action="/todos.json" method="post" class="new" use:enhance={{
    result: processNewTodoResult
  }}>
    <input type="text" name="text" aria-label="Add a todo" placeholder="+ type to add a todo" />
    
  </form>
  
  
  <label class="todo">
    <input type="checkbox" bind:checked={yes}>{Show}</label>
    
  {#each todos as  todo}
  {#if yes == true || (yes == false && todo.done == false)}
    <TodoItem
      {todo}
      processDeletedTodoResult={() => {
        todos = todos.filter(t => t.uid !== todo.uid);
      }}
      {processUpdatedTodoResult}
      
       />
       <!--{i = i+1}
       <div> Called {i}</div>-->
       { /if}
  {/each}
  

</div>
