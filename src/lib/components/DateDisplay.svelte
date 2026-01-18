<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	let date = $state('');
	let interval: number;

	function updateDate() {
		const now = new Date();
		date = now.toLocaleDateString('de-DE', { 
			weekday: 'long', 
			year: 'numeric', 
			month: 'long', 
			day: 'numeric' 
		});
	}

	onMount(() => {
		updateDate();
		interval = setInterval(updateDate, 60000);
	});

	onDestroy(() => {
		if (interval) clearInterval(interval);
	});
</script>

<div class="date">
	<div class="date-text">{date}</div>
</div>

<style>
	.date {
		text-align: center;
		margin-top: 1rem;
	}

	.date-text {
		font-size: 2rem;
		font-weight: 300;
		color: white;
		text-shadow: 0 2px 10px rgba(0, 0, 0, 0.5);
	}
</style>
