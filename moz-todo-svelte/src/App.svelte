<script>
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';

	const [send, receive] = crossfade({
		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});

	// Figure out how to store the user's todo input here, no change on refresh
	let todos = []
	let uid = todos.length + 1;

	function add(input) {
		const todo = {
			id: uid++,
			done: false,
			description: input.value
		};

		todos = [todo, ...todos];
		input.value = '';

	}
	
	let count = 0;
	
	function handleClick() {
		count += 1;
	}

	function remove(todo) {
		todos = todos.filter(t => t !== todo);
	}
</script>

<!--  
<header>
	<img src="images/thanku.png" alt="header image"/>
</header>
-->

<div class='board'>
	<input
		class="new-todo"
		placeholder="What are you most thankful for? A person, place, thing, skill..."
		on:keydown="{event => event.key === 'Enter' && add(event.target)}"
	>
</div>

<div class='left'>
		
	{#each todos.filter(t => !t.done) as todo (todo.id)}
		<label
			in:receive="{{key: todo.id}}"
			out:send="{{key: todo.id}}"
			animate:flip
		>
			{todo.description}
		</label>
	{/each}
</div>

<style>
	header {
		text-align: center;
		margin-top: 2rem;
		font-size: 3rem;
	}
	
	.new-todo {
		font-size: 1em;
		width: 100%;
		margin: 2em 0 1em 0;
	}

	.board {
		max-width: 36em;
		margin: 0 auto;
	}

	.left {
		text-align: center;
		width: 100%;
		padding: 0 1em 0 0;
		box-sizing: border-box;
	}

	h2 {
		font-size: 2em;
		font-weight: 200;
		user-select: none;
	}

	label {
		top: 0;
		left: 0;
		display: inline-block;
		font-size: 1rem;
		line-height: 1;
		padding: 0.5em;
		margin: auto 0.5em auto;
		margin-bottom: 1%;
		border-radius: 5px;
		background-color: #eee;
		user-select: none;
	}

	input { margin: 0 }

	.right label {
		background-color: rgb(180,240,100);
	}

	button {
		float: right;
		height: 1em;
		box-sizing: border-box;
		padding: 0 0.5em;
		line-height: 1;
		background-color: transparent;
		border: none;
		color: rgb(170,30,30);
		opacity: 0;
		transition: opacity 0.2s;
	}

	label:hover button {
		opacity: 1;
	}
</style>