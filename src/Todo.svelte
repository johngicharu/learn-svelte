<script>
  import { createEventDispatcher } from "svelte";
  import { fade } from "svelte/transition";

  const dispatcher = createEventDispatcher();
  export let todo = {};
  let isActive = false;
  let changed = false;

  const updateTodo = (e) => {
    if (e.target.type === "checkbox" && !changed) {
      todo.done = !todo.done;
    } else {
      isActive = false;
    }
    dispatcher("updateTodo", { todo: todo });
  };

  const deleteTodo = (e) => {
    dispatcher("deleteTodo", { todo: todo });
  };

  const activate = () => {
    isActive = true;
    changed = true;
  };
</script>

<article transition:fade>
  <div>
    <input
      on:change={updateTodo}
      type="checkbox"
      name={todo.id}
      id={todo.id}
      checked={todo.done}
    />
  </div>
  <div>
    <input
      on:input={activate}
      type="text"
      name={todo.body}
      bind:value={todo.body}
    />
    <span
      class="strike"
      style={`${todo.done ? "display: block" : "display: none"}`}
    />
  </div>
  <div>
    <button on:click={deleteTodo}>❌</button>
    {#if isActive}
      <button on:click={updateTodo}>✔️</button>
    {/if}
  </div>
</article>

<style>
  article {
    width: 90%;
    margin: 5px auto;
    display: flex;
    justify-content: flex-start;
    align-items: center;
  }

  div {
    margin: 0 5px;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
  }

  input {
    outline: none;
    border: none;
    padding: 0;
    margin: 0;
  }

  span {
    position: absolute;
    top: 55%;
    left: 0%;
    /* transform: translate(-50%, -50%) scale(0, 1); */
    display: block;
    z-index: 3;
    width: 100%;
    height: 2px;
    background-color: dodgerblue;
    transform-origin: left;
    animation: draw 300ms ease-in-out forwards;
  }

  @keyframes draw {
    0% {
      transform: scale(0, 1);
    }
    100% {
      transform: scale(1, 1);
    }
  }

  button {
    outline: none;
    background: none;
    border: none;
  }
</style>
