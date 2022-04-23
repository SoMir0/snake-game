<script lang="ts">
	import { onMount, tick } from 'svelte';
	// variable declarations
	let gridSize = 20;
	let tickRate = 500;
	let maxSpeed = 75;
	type Cell = 'empty' | 'snake' | 'food';
	let grid : Cell[][] = [...Array(gridSize)].map(() => [...Array(gridSize)].map(() => "empty"));
	grid[5][10] = 'food';
	let snakes: Array<[number, number]> = [[12, 13]];
	let end = false;

	let length = 1;

	let xy = 0;
	let dir = 1;
	// functions and onmount
	function handleKeydown(e : any) {
		switch(e.keyCode){
			case 87:
				if(xy == 0) break;
				xy = 0;
				dir = -1;
				break;
			case 82:
				window.location = window.location;
				break;
			case 65:
				if(xy == 1) break;
				xy = 1;
				dir = -1;
				break;
			case 83:
				if(xy == 0) break;
				xy = 0;
				dir = 1;
				break;
			case 68:
				if(xy == 1) break;
				xy = 1;
				dir = 1;
				break;
			default:
				break;
		}
	}

	function randomFood() {
		grid[Math.floor(Math.random() * gridSize)][Math.floor(Math.random() * gridSize)] = 'food';
		grid[Math.floor(Math.random() * gridSize)][Math.floor(Math.random() * gridSize)] = 'food';
	}

	function handleTile(nextPos) {
		if(!grid[nextPos[0]][nextPos[1]] || grid[nextPos[0]][nextPos[1]] == 'snake') {
			end = true;
		}
		else if(grid[nextPos[0]][nextPos[1]] == 'food') {
			randomFood();
			length += 1;
			tickRate *= 0.9;
		}
	}

	const fn = (n: number) => {
		setTimeout(() => {
			if(end) return;
			
			const lastPos = snakes[0];

			const nextPos = [...lastPos] as [number, number];
			nextPos[xy] += dir;

			snakes.unshift(nextPos);

			handleTile(nextPos);
			
			grid[nextPos[0]][nextPos[1]] = 'snake';

			if (snakes.length > length) {
				let delSnake = snakes.pop();

				grid[delSnake[0]][delSnake[1]] = 'empty';
			}
			fn(Math.max(tickRate, maxSpeed));
		}, n);
	}

	onMount(() => {
		fn(tickRate);
	});
</script>

<svelte:window on:keydown={handleKeydown}/>

<main>
	{#each grid as row, i}
		<div class="row">
			{#each row as cell, k}
			<div class={`square ${cell}`} />
			{/each}
		</div>
	{/each}
</main>

<style>
	:global(body), :global(html) {
		background: #1B1A17;
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	main {
		box-shadow: #706f2a 0px 0px 30px;
	}

	.square {
		width: 20px;
		height: 20px;
	}

	.empty {
		background-color: #F0A500;
	}

	.snake {
		background-color: #E45826;
	}

	.food {
		background-color: #E6D5B8;
	}

	.row {
		display: flex;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
