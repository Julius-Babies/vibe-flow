<script lang="ts">
	import { onMount } from 'svelte';

	let backgroundUrl = $state('');
	let imageTitle = $state('');
	let loading = $state(true);

	async function fetchBingImage() {
		try {
			loading = true;
			
			// Bing Image of the Day API
			const response = await fetch(
				'https://www.bing.com/HPImageArchive.aspx?format=js&idx=0&n=1&mkt=en-US'
			);
			
			if (!response.ok) {
				throw new Error('Failed to fetch Bing image');
			}
			
			const data = await response.json();
			
			if (data.images && data.images.length > 0) {
				const image = data.images[0];
				backgroundUrl = `https://www.bing.com${image.url}`;
				imageTitle = image.title || '';
			}
			
			loading = false;
		} catch (e) {
			console.error('Error fetching Bing image:', e);
			// Use a fallback gradient
			backgroundUrl = '';
			loading = false;
		}
	}

	onMount(() => {
		fetchBingImage();
		// Refresh image once per day
		const interval = setInterval(fetchBingImage, 86400000);
		return () => clearInterval(interval);
	});
</script>

<div 
	class="background" 
	class:loading={loading}
	style={backgroundUrl ? `background-image: url(${backgroundUrl})` : ''}
	role="img"
	aria-label={imageTitle}
>
	{#if loading}
		<div class="loading-overlay">Loading background...</div>
	{/if}
</div>

<style>
	.background {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-size: cover;
		background-position: center;
		background-repeat: no-repeat;
		background-color: #1a1a2e;
		z-index: -1;
	}

	.background::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background: linear-gradient(
			to bottom,
			rgba(0, 0, 0, 0.2) 0%,
			rgba(0, 0, 0, 0.4) 100%
		);
	}

	.loading {
		background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
	}

	.loading-overlay {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		color: white;
		font-size: 1.5rem;
		opacity: 0.6;
	}
</style>
