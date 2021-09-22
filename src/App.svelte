<script>
	import { onMount } from 'svelte';
	
	const SPEED = 7;

	const patterns = [
		{name: 'ON', pattern: 'm'},
		{name: 'FLUORESCENT FLICKER', pattern: 'mmamammmmammamamaaamammma'},
		{name: 'FLICKER 1', pattern: 'mmnmmommommnonmmonqnmmo'},
		{name: 'SLOW STRONG PULSE', pattern: 'abcdefghijklmnopqrstuvwxyzyxwvutsrqponmlkjihgfedcba'},
		{name: 'CANDLE 1', pattern: 'mmmmmaaaaammmmmaaaaaabcdefgabcdefg'},
		{name: 'FAST STROBE', pattern: 'mamamamamama'},
		{name: 'GENTLE PULSE', pattern: 'jklmnopqrstuvwxyzyxwvutsrqponmlkj'},
		{name: 'FLICKER 2', pattern: 'nmonqnmomnmomomno'},
		{name: 'CANDLE 2', pattern: 'mmmaaaabcdefgmmmmaaaammmaamm'},
		{name: 'CANDLE 3', pattern: 'mmmaaammmaaammmabcdefaaaammmmabcdefmmmaaaa'},
		{name: 'SLOW STROBE', pattern: 'aaaaaaaazzzzzzzz'},
		{name: 'SLOW PULSE NOT FADE TO BLACK', pattern: 'abcdefghijklmnopqrrqponmlkjihgfedcba'},
	];
	
	const alpha = 'abcdefghijklmnopqrstuvwxyz'.split('');
	
	let choice = 1;
	let counter = 0;
	let pointer = 0;
	let value = 0;
	let op = 0;
	
	const mapValue = function (value, in_min, in_max, out_min, out_max) {
		return ((value - in_min) * (out_max - out_min)) / (in_max - in_min) + out_min;
	};
	
	const handleClick = () => {
		choice++;
		if(choice>=patterns.length){
			choice = 0;
		}
	}
	
	onMount(() => {
		let frame;

		function loop() {
			frame = requestAnimationFrame(loop);

			counter++;
			if (counter % SPEED === 0) {
				value = patterns[choice].pattern[pointer];
				const ai = alpha.indexOf(value);
				op = mapValue(ai, 0, 26, 0, 1);
				pointer++;
				if (pointer >= patterns[choice].pattern.length) {
					pointer = 0;
				}
			}
		}

		loop();

		return () => cancelAnimationFrame(frame);
	});
</script>

<div class="frame">
	<div class="light" style="opacity:{op}" on:click={handleClick}></div>
	<div class="light" style="opacity:{op}" on:click={handleClick}></div>
	<div class="light" style="opacity:{op}" on:click={handleClick}></div>
</div>

<p>
	{patterns[choice].name}
</p>

<style>
	:global(body) {
		overflow: hidden;
		background: #131313;
	}
	
	p {
	color: white;
		text-align: center;
	}
	
	.frame {
		border: 4px solid #525252;
		border-radius: 5px;
		background: #232323;
		box-shadow: rgba(255, 255, 255, 0.5) 0px 1px 4px;
	}
	
	.light {
		cursor: pointer;
		background: #f0e68c;
		height: 50px;
		margin: 10px 10px 13px 10px;
		filter: blur(10px);
	}
	
	.light:last-child {
		margin-bottom:15px;
	}
</style>
