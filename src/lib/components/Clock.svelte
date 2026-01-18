<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	let time = $state('');
	let interval: number;

	function updateTime() {
		const now = new Date();
		time = now.toLocaleTimeString('de-DE', { 
			hour: '2-digit', 
			minute: '2-digit'
		});
	}

	onMount(() => {
		updateTime();
		interval = setInterval(updateTime, 1000);
	});

	onDestroy(() => {
		if (interval) clearInterval(interval);
	});
</script>

<div class="clock">
	<div class="time">{time}</div>
</div>

<style>
	.clock {
		text-align: center;
	}

	.time {
		font-size: 6rem;
		font-weight: 300;
		color: white;
		text-shadow: 0 2px 10px rgba(0, 0, 0, 0.5);
		letter-spacing: 0.1em;
	}
</style>
