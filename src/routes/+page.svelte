<script lang="ts">
	import Header from '../components/layout/Header.svelte';
	import Footer from '../components/layout/Footer.svelte';
	import Button from '../components/Button.svelte';
	import { onMount, tick } from 'svelte';
	import { fade } from 'svelte/transition';

	let velocityX = 0;
	let velocityY = 1;
	let gameTick: number;
	let changedDirection = false;
	let initialValue = [
		{ x: 7, y: 2 },
		{ x: 7, y: 1 }
	];
	let snake = [...initialValue];
	let game = true;

	$: head = snake[0];
	$: tail = snake[snake.length - 1];
	
	let gameTable: Array<Array<string>> = [
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', 's', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', 's', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
		['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0']
	];

	$: {
		for (let i = snake.length - 1; i >= 0; i--) {
			if (i == 0) {
				if (snake[i].x >= 0 && snake[i].x <= 14) {
					if (snake[i].y >= 0 && snake[i].y <= 14) {
						gameTable[snake[i].y][snake[i].x] = 's';
					}
				}
				continue;
			}
			gameTable[snake[i].y][snake[i].x] = 's';
		}
	}

	function moveSnake() {
		gameTable[tail.y][tail.x] = '0';
		for (let i = snake.length - 1; i > 0; i--) {
			snake[i] = snake[i - 1];
		}
		snake[0] = {
			x: head.x + velocityX,
			y: head.y + velocityY
		};
	}

	function verifyDefeat(): boolean {
		if (head.x < 0 || head.x > 14) {
			return true;
		}

		if (head.y < 0 || head.y > 14) {
			return true;
		}

		if (snake.includes(head, 1)) {
			return true;
		}

		return false;
	}

	function startGame() {
		velocityX = 0;
		velocityY = 1;
		const gameTick = setInterval(async () => {
			moveSnake();
			changedDirection = false;
			await tick();
			if (verifyDefeat()) {
				clearInterval(gameTick);
				game = false;
			}
		}, 250);
	}
	
	function changeDirection(event: { key: string; }) {
		if (!changedDirection) {

			if (event.key.toLowerCase() == 'w' || event.key == "ArrowUp") {
				if (velocityY == 0) {
					velocityX = 0;
					velocityY = -1;
					changedDirection = true;
				}
			}
			
			if (event.key.toLowerCase() == 'a') {
				if (velocityX == 0) {
					velocityX = -1;
					velocityY = 0;
					changedDirection = true;
				}
			}
			
			if (event.key.toLowerCase() == 's') {
				if (velocityY == 0) {
					velocityX = 0;
					velocityY = 1;
					changedDirection = true;
				}
			}
			
			if (event.key.toLowerCase() == 'd') {
				if (velocityX == 0) {
					velocityX = 1;
					velocityY = 0;
					changedDirection = true;
				}
			}
		}
			
		

	}

	onMount(() => {
		startGame();
		return () => {
			clearInterval(gameTick);
		};
	});


</script>

<Header />

<main>
	<section>
		<div class="jogo">
			{#if game}
				{#each gameTable as row, rowIndex ('row' + rowIndex)}
					<div class="row">
						{#each row as cell, cellIndex ('cell' + rowIndex + cellIndex)}
							<div class={`cell ${cell == '0' ? 'empty' : cell == 's' ? 'snake' : 'fruit'}`}>
							</div>
						{/each}
					</div>
				{/each}
			{:else}
				<div class="lost" in:fade={{ duration: 250 }}>
					<p>You lost</p>
					<Button
						click={(e) => {
							e.preventDefault();
							game = true;
							snake = [...initialValue];
							gameTable = [
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', 's', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', 's', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0'],
								['0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0']
							];
							startGame();
						}}>Play again</Button
					>
				</div>
			{/if}
		</div>
		<div class="controles"></div>
	</section>
</main>

<svelte:window on:keydown={changeDirection}/>

<Footer />

<style>
	/* 
    red #ED1C24
    blue #235789
    yellow #F1D302
    white #FDFFFC
    black #020100
    */

	main {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-top: 40px;
		background-color: #fdfffc;
	}

	.row {
		display: flex;
	}

	.cell {
		width: 16px;
		height: 16px;
		border: solid 1px #235789;
		background-color: #fdfffc;
		box-sizing: border-box;
	}

	.snake {
		background-color: #f1d302;
	}

	.fruit {
		background-color: #ed1c24;
	}

	.lost {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 240px;
		height: 240px;
		background-color: #ed1c24;
	}
</style>
