<script lang="ts">
	// @ts-ignore
	import Tick from '@pqina/flip';
	import '@pqina/flip/dist/flip.min.css';
	import { onMount } from 'svelte';

	const say = (str: string): void => {
		const uttr = new SpeechSynthesisUtterance(str);
		uttr.lang = 'en-US';
		speechSynthesis.speak(uttr);
	};

	let minitues = 10;
	let remainingSec = 0;

	const calcRemainingTime = (remainingSec: number) => {
		const [minTenPlace, minOnePlace] = String(Math.floor(remainingSec / 60)).padStart(2, '0');
		const sec = remainingSec % 60;
		const [SecTensPlace, SecOnesPlace] = String(sec).padStart(2, '0').split('');

		return { min: [minTenPlace, minOnePlace], sec: [SecTensPlace, SecOnesPlace] };
	};

	const countDown = () => {
		remainingSec = minitues * 60;
		setInterval(() => {
			tickMin.value = calcRemainingTime(remainingSec).min;
			tickSec.value = calcRemainingTime(remainingSec).sec;
			remainingSec--;
		}, 1000);
		setInterval(() => {
			say('mob next');
		}, minitues * 60 * 1000);
	};

	let tickMinEle: HTMLDivElement;
	let tickSecEle: HTMLDivElement;
	let tickMin: { value: string[] };
	let tickSec: { value: string[] };
	onMount(() => {
		tickMin = Tick.DOM.create(tickMinEle);
		tickSec = Tick.DOM.create(tickSecEle);
	});
</script>

<svelte:head>
	<title>mob-next</title>
	<meta name="robots" content="noindex nofollow" />
</svelte:head>

<label><input type="number" bind:value={minitues} min="1" />分</label>
<button
	on:click={() => {
		countDown();
	}}>Start</button
>
<br />

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

<style>
	@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono&display=swap');

	:global(*) {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
	}

	.tick {
		font-family: 'Roboto Mono', monospace;
		font-size: 5rem;
		display: inline-block;
	}

	.colon {
		font-family: 'Roboto Mono', monospace;
		font-size: 5rem;
	}
</style>
