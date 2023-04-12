<script lang="ts">
	// @ts-ignore
	import Tick from '@pqina/flip';
	import '@pqina/flip/dist/flip.min.css';
	import { onDestroy, onMount } from 'svelte';

	let passege = 'mob next';

	const say = (str: string): void => {
		const uttr = new SpeechSynthesisUtterance(str);

		//strが英語か日本語か
		if (/^[\d A-Za-z]+$/.test(str)) {
			uttr.lang = 'en-US';
		} else {
			uttr.lang = 'jp-JP';
		}

		speechSynthesis.speak(uttr);
	};

	const calcRemainingTime = (remainingSec: number) => {
		const [minTenPlace, minOnePlace] = String(Math.floor(remainingSec / 60)).padStart(2, '0');
		const sec = remainingSec % 60;
		const [SecTensPlace, SecOnesPlace] = [...String(sec).padStart(2, '0')];

		return { min: [minTenPlace, minOnePlace], sec: [SecTensPlace, SecOnesPlace] };
	};

	let startButton = true;
	let minitues = 10;
	let remainingSec = 0;
	let timerId: NodeJS.Timeout;

	const countDown = () => {
		say(passege);
		startButton = false;
		remainingSec = minitues * 60 - 1;
		timerId = setInterval(() => {
			tickMin.value = calcRemainingTime(remainingSec).min;
			tickSec.value = calcRemainingTime(remainingSec).sec;

			if (remainingSec === 0) {
				say('');
				say(passege);
				remainingSec = minitues * 60;
			} else {
				remainingSec--;
			}
		}, 1000);
	};

	let tickMinEle: HTMLDivElement;
	let tickSecEle: HTMLDivElement;
	let tickMin: { value: string[] };
	let tickSec: { value: string[] };

	onMount(() => {
		tickMin = Tick.DOM.create(tickMinEle);
		tickSec = Tick.DOM.create(tickSecEle);
	});

	onDestroy(() => {
		clearInterval(timerId);
	});
</script>

<svelte:head>
	<title>mob-next</title>
	<meta name="robots" content="noindex nofollow" />
</svelte:head>

<div class="container">
	<div class="tickContainer">
		<div class="tickMin tick" data-value="[0,0]" data-did-init="setupFlip" bind:this={tickMinEle}>
			<div data-repeat="true" aria-hidden="true">
				<span data-view="flip" />
			</div>
		</div>
		<span class="colon">:</span>
		<div class="tickSec tick" data-value="[0,0]" data-did-init="setupFlip" bind:this={tickSecEle}>
			<div data-repeat="true" aria-hidden="true">
				<span data-view="flip" />
			</div>
		</div>
	</div>

	<label class="inputContainer"
		><input
			type="number"
			bind:value={minitues}
			min="1"
			max="99"
			class="minitues"
			disabled={!startButton}
		/>min</label
	>

	{#if startButton}
		<div>
			<input type="text" class="passage" bind:value={passege} />
			<button
				class="startButton"
				on:click={() => {
					countDown();
				}}>Start</button
			>
		</div>
	{/if}
</div>

<style>
	@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono&display=swap');

	:global(*) {
		font-family: 'Roboto Mono', monospace;
		margin: 0;
		padding: 0;
		box-sizing: border-box;
	}

	.container {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		min-height: 100dvh;
		min-width: 100dvw;
	}

	.inputContainer {
		font-size: 2rem;
		margin: 20px 0;
	}

	.minitues {
		width: 3.5rem;
		text-align: center;
	}

	.passage {
		width: 16rem;
		text-align: center;
	}

	input {
		font-size: 2rem;
		border: none;
		border-bottom: 2px solid #000;
	}

	input:disabled {
		background-color: inherit;
		color: inherit;
	}

	.tick {
		font-size: min(10rem, 13dvw);
		display: inline-block;
	}

	.colon {
		font-size: 5rem;
	}

	.startButton {
		font-size: 1.25rem;
		padding: 5px 10px;
		border: none;
		border-radius: 5px;
		background-color: #000;
		color: #fff;
	}
</style>
