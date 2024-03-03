<script lang="ts">
	import { onMount, createEventDispatcher } from "svelte"
	import { sineOut, backOut } from "svelte/easing"

	const scaleCSS = (node, { duration = 200, delay = 0 }) => ({
	duration,
	delay,
	css: (t) => `transform: scale(${sineOut(t)})`,
	});

	const scaleJS = (node, { duration = 200, delay = 0 }) => ({
	duration,
	delay,
	tick: (t) => {
		node.style.transform = `scale(${sineOut(t)})`
	},
	});

	const jiggleCSS = (node, { duration = 1000, delay = 0 }) => ({
	duration,
	delay,
	css: (t) => {
		const scale = 1 + backOut(1 - t)
		const angle = Math.sin(t * 3.14 * 2) * 30
		return `transform: scale(${scale}) rotate(${angle}deg)`
	},
	});

	const jiggleJS = (node, { duration = 1000, delay = 0 }) => {
	return {
		duration,
		delay,
		tick: (t) => {
		const scale = 1 + backOut(1 - t) * 0.75
		const angle = Math.sin(t * 3.14 * 2) * 30
		node.style.transform = `scale(${scale}) rotate(${angle}deg)`
		},
	}
	}
	
	export let timer
	export let urgent = 5
	$: countdown = timer - urgent

	let isUrgent = false
	let isCountdown = true


</script>

<div>
	<svg viewBox="-15.5 -15.5 30 30">
		{#if isUrgent}
		<g transition:scale={{ duration: 200 }}>
			<circle r="10" fill="#f9c801" stroke="#3c3c3c" />
			<g
				text-anchor="middle"
				dominant-baseline="central"
				font-family="sans-serif"
				paint-order="stroke"
				fill="#ffffff"
				stroke="#3c3c3c"
				stroke-width="1.65"
				stroke-linejoin="round"
				stroke-linecap="round"
				font-size="14"
				>
				{#key $timer - 1}
				<g in:jiggle>
					<text>{$timer - 1}</text>
				</g>
				{/key}
			</g>
		</g>
		{/if}
		{#if isCountdown}
		<g in:scale={{ delay: 200 }}>
			<g
				text-anchor="middle"
				dominant-baseline="central"
				font-family="sans-serif"
				paint-order="stroke"
				fill="#ffffff"
				stroke="#3c3c3c"
				stroke-width="1.65"
				stroke-linejoin="round"
				stroke-linecap="round"
				font-size="14"
				>
				<text>{$timer}</text>
			</g>
		</g>
		{/if}
	</svg>
</div>

<style>
	div {
		margin-left: auto;
		margin-right: auto;
		width: 12rem;
	}
	
	svg, button {
		display: block;	
	}
	
	div > svg {
		width: 100%;
		height: auto;
	}
	
	button {
		cursor: pointer;
		width: 4rem;
		margin-left: auto;
		margin-right: auto;
		background: none;
		border: none;
	}
	
	button:active {
		background: unset;
	}
	
	button > svg {
		width: 100%;
		height: auto;
	}
</style>