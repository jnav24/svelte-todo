<script>
    const ENTER_KEY = 13;
    const ESCAPE_KEY = 27;

    let newTodo = '';
	let todos = [
	    {
	        id: 1,
	        completed: false,
	        title: 'Go to Store',
	        editing: false,
	    },
	    {
            id: 2,
            completed: false,
            title: 'Finish Svelte',
            editing: false,
        },
        {
            id: 3,
            completed: false,
            title: 'Take over the world',
            editing: false,
        },
	];

	$: todosRemaining = todos.filter((obj) => !obj.completed).length;
	$: filteredTodos = todos;

	const addTodo = (event) => {
	    if (event.which === ENTER_KEY) {
            todos = [
                ...todos,
                {
                    id: getNewId(),
                    completed: false,
                    title: newTodo,
                    editing: false,
                }
            ];
            newTodo = '';
	    }
	};

	const getNewId = () => {
	    if (todos.length) {
	        return Number(todos[todos.length - 1].id) + 1;
	    }

	    return 1;
	};

	const deleteTodo = (id) => {
	    todos = todos.filter((obj) => obj.id !== id);
	};

	const checkAllTodos = (e) => {
	    todos = todos.map((todo) => {
	        todo.completed = e.target.checked;
	        return todo;
	    });
	};

	const clearCompleted = () => {
	    todos = todos.filter((obj) => !obj.completed);
	};

	const updateFilter = (value) => {
        switch (value) {
            case 'active':
                filteredTodos = todos.filter((obj) => !obj.completed);
                break;
            case 'completed':
                filteredTodos = todos.filter((obj) => obj.completed);
                break;
            default:
                filteredTodos = todos;
                break;
        }
	};

	const editTodo = (todo) => {
	    todo.editing = true;
	    todos = todos;
	};

	const doneEdit = (todo) => {
	    todo.editing = false;
        todos = todos;
	}

	const doneEditKeydown = (e, todo) => {
	    if (e.which === ENTER_KEY) {
	        doneEdit(todo);
	    }
	};
</script>

<style lang="scss">
.container {
    max-width: 600px;
    margin: 0 auto;
}

.logo {
    display: block;
    margin: 20px auto;
    height: 75px;
}

.todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
}

.todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: 0.3s;
}

.remove-item {
    cursor: pointer;
    margin-left: 14px;

    &:hover {
        color: black;
    }
}

.todo-item-left {
    display: flex;
    align-items: center;
}

.todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
}

.todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;

    &:focus {
        outline: none;
    }
}

.completed {
    text-decoration: line-through;
    color: grey;
}

.extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;

    input {
        margin-right: 8px;
    }
}

button {
    font-size: 14px;
    background-color: white;
    appearance: none;

    &:hover {
        background: salmon;
    }

    &:focus {
        outline: none;
    }
}

.active {
    background: salmon;
}
</style>

<div class="container">
    <img src={'img/svelte-logo.svg'} alt="Svelte Logo" class="logo">

    <input
        on:keydown={addTodo}
        bind:value={newTodo}
        type="text"
        class="todo-input"
        placeholder="What needs to be done">

    {#each filteredTodos as todo}
    <div class="todo-item">
        <div class="todo-item-left">
            <input type="checkbox" bind:checked={todo.completed}>

            {#if !todo.editing}
                <div on:dblclick={() => editTodo(todo)} class="todo-item-label" class:completed={todo.completed}>{todo.title}</div>
            {:else}
                <input type="text" class="todo-item-edit" bind:value={todo.title} autofocus on:blur={() => doneEdit(todo)} on:keydown={() => doneEditKeydown(event, todo)}>
            {/if}
        </div>

        <div class="remove-item" on:click={() => deleteTodo(todo.id)}>
            &times;
        </div>
    </div>
    {/each}

    <div class="extra-container">
        <div>
            <label for="">
                <input type="checkbox" on:change={checkAllTodos}> Check All
            </label>
        </div>

        <div>{todosRemaining} items left</div>
    </div>

    <div class="extra-container">
        <div>
            <button on:click={() => updateFilter('all')}>All</button>
            <button on:click={() => updateFilter('active')}>Active</button>
            <button on:click={() => updateFilter('completed')}>Completed</button>
        </div>

        <div>
            <button on:click={() => clearCompleted()}>Clear Completed</button>
        </div>
    </div>
</div>
