<script>
  import Todo from "./Todo.svelte";

  $: todos = [];

  fetch("http://localhost:3000/todos")
    .then(async (res) => {
      const data = await res.json();
      todos = data.sort((a, b) => b.id - a.id);
    })
    .catch((err) => console.log(err));

  const updateTodo = (e) => {
    fetch(`http://localhost:3000/todos/${e.detail.todo.id}`, {
      method: "PUT",
      headers: {
        "Content-type": "application/json; charset=UTF-8",
      },
      body: JSON.stringify(
        todos.filter((todo) => todo.id === e.detail.todo.id)[0]
      ),
    }).catch((err) => console.log(err));
  };

  const deleteTodo = (e) => {
    todos = todos.filter((todo) => todo.id !== e.detail.todo.id);

    fetch(`http://localhost:3000/todos/${e.detail.todo.id}`, {
      method: "DELETE",
    }).catch((err) => console.log(err));
  };

  const clearTodos = () => {
    todos.forEach((todo) => {
      deleteTodo({ detail: { todo } });
    });
  };

  const addTodo = (e) => {
    e.preventDefault();

    const newTodo = {
      body: e.target["newTodo"].value,
      id: todos.length + 1,
      done: false,
      user: 1,
    };

    todos = [newTodo, ...todos];

    fetch(`http://localhost:3000/todos`, {
      method: "POST",
      headers: {
        "Content-type": "application/json; charset=UTF-8",
      },
      body: JSON.stringify(newTodo),
    })
      .then(() => {
        e.target.reset();
      })
      .catch((err) => console.log(err));
  };
</script>

<main>
  <h1>Todo App</h1>
  <div>
    <form on:submit={addTodo}>
      <input type="text" name="newTodo" placeholder="What next?" />
      <button class="btn one" type="submit">Add todo</button>
    </form>
    {#if todos.length > 1}
      <button class="btn all" on:click={clearTodos}>Clear Todos</button>
    {/if}
  </div>
  {#if todos.length === 0}
    <p>Sorry to burst your bubble but you have nothing to do...I mean at all</p>
  {:else}
    {#each todos as todo (todo.id)}
      <Todo {todo} on:updateTodo={updateTodo} on:deleteTodo={deleteTodo} />
    {/each}
  {/if}
</main>

<style>
  main {
    font-family: cursive;
    width: 1024px;
    margin: 0 auto;
  }

  div {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    width: 95%;
    padding: 0;
  }

  .btn {
    margin-left: 10px;
    border-radius: 4px;
    border: none;
    color: #fffffa;
  }

  .one {
    background-color: #48f1d5;
  }

  .all {
    background-color: red;
  }

  p {
    font-size: 0.8rem;
  }

  @media (max-width: 1200px) {
    main {
      width: 720px;
    }
  }
</style>
