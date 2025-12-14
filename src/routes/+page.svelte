<script lang="ts">
	import { browser } from '$app/environment';

	$effect(() => {
		updateBubbles();

		window.addEventListener('resize', updateBubbles);
		window.addEventListener('orientationchange', updateBubbles);

		window.visualViewport?.addEventListener('resize', updateBubbles);

		return () => {
			window.removeEventListener('resize', updateBubbles);
			window.removeEventListener('orientationchange', updateBubbles);
			window.visualViewport?.removeEventListener('resize', updateBubbles);
		};
	});

	function generateBubbles(rowCount: number, columnCount: number) {
		return Array.from({ length: rowCount }, () => Array.from({ length: columnCount }, () => false));
	}

	function getBubbles() {
		const screenWidth = browser ? window.innerWidth : 0;
		const screenHeight = browser ? window.innerHeight : 0;

		const rows = Math.floor(screenHeight / bubbleSize);
		const cols = Math.floor(screenWidth / bubbleSize);

		return generateBubbles(rows, cols);
	}

	function updateBubbles() {
		bubbles = getBubbles();
	}

	function pop(rowIndex: number, columnIndex: number) {
		if (!bubbles[rowIndex][columnIndex]) {
			if (browser && typeof navigator !== 'undefined' && navigator.vibrate) {
				navigator.vibrate([12, 8, 12]);
			}

			bubbles[rowIndex][columnIndex] = true;
		}
	}

	const bubbleSize = 60;

	let bubbles = $state(getBubbles());
</script>

<svelte:head>
	<title>POP IT!</title>
	<meta
		name="description"
		content="Play Pop It online for free! Relax, reduce stress, and enjoy satisfying bubble popping games on desktop and mobile. No download required."
	/>
	<meta
		name="keywords"
		content="pop it online, pop it game, pop it toy, stress relief game, bubble popping, relaxing games, sensory games"
	/>
</svelte:head>

{#each bubbles as isPoppedRow, i}
	<div class="row">
		{#each isPoppedRow as isCellPopped, j}
			<button
				class="circle"
				class:popped={isCellPopped}
				style="--bubbleSize: {bubbleSize}px"
				aria-label={isCellPopped ? 'Popped bubble' : 'Unpopped bubble'}
				onpointerdown={() => pop(i, j)}
			>
				<span class="inner-shadow"></span>
			</button>
		{/each}
	</div>
{/each}

<style>
	:global {
		*,
		*::before,
		*::after {
			box-sizing: border-box;
			margin: 0;
			padding: 0;
		}

		html,
		body {
			height: 100%;
			background-color: hsl(var(--bubble-hue), 70%, 60%);
		}

		:root {
			--bubble-hue: 210;
		}
	}

	.row {
		display: flex;
		justify-content: space-between;
	}

	.circle {
		width: var(--bubbleSize);
		aspect-ratio: 1;
		border-radius: 50%;
		border-width: 0;
		margin: 1px;

		/* background-color: hsl(var(--bubble-hue), 70%, 60%); */
		background: radial-gradient(
			circle at 33% 33%,
			hsl(var(--bubble-hue), 100%, 68%),
			hsl(var(--bubble-hue), 100%, 26%)
		);
	}

	.popped {
		background: radial-gradient(
			circle at 33% 33%,
			hsl(var(--bubble-hue), 100%, 26%),
			hsl(var(--bubble-hue), 70%, 60%)
		);
	}
</style>
