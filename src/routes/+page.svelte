<script lang="ts">
	import BingBackground from '$lib/components/BingBackground.svelte';
	import Clock from '$lib/components/Clock.svelte';
	import DateDisplay from '$lib/components/DateDisplay.svelte';
	import Weather from '$lib/components/Weather.svelte';
	import News from '$lib/components/News.svelte';
</script>

<BingBackground />

<div class="container">
	<div class="main-content">
		<Clock />
		<DateDisplay />
	</div>
	
	<div class="widgets">
		<div class="widget">
			<Weather />
		</div>
		<div class="widget">
			<News />
		</div>
	</div>
</div>

<style>
	:global(body) {
		margin: 0;
		padding: 0;
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
		overflow: hidden;
		height: 100vh;
	}

	.container {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		min-height: 100vh;
		padding: 2rem;
		box-sizing: border-box;
	}

	.main-content {
		flex: 1;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	.widgets {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
		gap: 1.5rem;
		max-width: 1200px;
		width: 100%;
		margin: 0 auto;
	}

	.widget {
		animation: fadeIn 0.5s ease-in;
	}

	@keyframes fadeIn {
		from {
			opacity: 0;
			transform: translateY(10px);
		}
		to {
			opacity: 1;
			transform: translateY(0);
		}
	}

	@media (max-width: 768px) {
		.container {
			padding: 1rem;
		}
		
		.widgets {
			grid-template-columns: 1fr;
		}
	}
</style>
