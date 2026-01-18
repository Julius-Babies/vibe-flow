<script lang="ts">
	import { onMount } from 'svelte';

	interface NewsItem {
		title: string;
		date: string;
	}

	interface RSSItem {
		title?: string;
		pubDate?: string;
	}

	interface RSS2JSONResponse {
		items?: RSSItem[];
	}

	let news = $state<NewsItem[]>([]);
	let currentIndex = $state(0);
	let loading = $state(true);
	let error = $state('');

	async function fetchNews() {
		try {
			loading = true;
			error = '';
			
			// Using Tagesschau RSS feed via RSS2JSON service
			const response = await fetch(
				'https://api.rss2json.com/v1/api.json?rss_url=https://www.tagesschau.de/xml/rss2/'
			);
			
			if (!response.ok) {
				throw new Error('News fetch failed');
			}
			
			const data: RSS2JSONResponse = await response.json();
			
			if (data.items && Array.isArray(data.items)) {
				news = data.items
					.slice(0, 10)
					.filter((item): item is RSSItem => 
						typeof item?.title === 'string' && typeof item?.pubDate === 'string'
					)
					.map((item) => ({
						title: item.title!,
						date: new Date(item.pubDate!).toLocaleDateString('de-DE', {
							day: '2-digit',
							month: '2-digit',
							hour: '2-digit',
							minute: '2-digit'
						})
					}));
			}
			
			loading = false;
		} catch (e) {
			error = 'Could not load news';
			loading = false;
		}
	}

	onMount(() => {
		fetchNews();
		
		// Refresh news every 10 minutes
		const newsInterval = setInterval(fetchNews, 600000);
		
		// Rotate news every 10 seconds
		const rotateInterval = setInterval(() => {
			if (news.length > 0) {
				currentIndex = (currentIndex + 1) % news.length;
			}
		}, 10000);
		
		return () => {
			clearInterval(newsInterval);
			clearInterval(rotateInterval);
		};
	});
</script>

<div class="news">
	{#if loading}
		<div class="loading">Loading news...</div>
	{:else if error}
		<div class="error">{error}</div>
	{:else if news.length > 0}
		<div class="news-container">
			<div class="news-header">Tagesschau</div>
			<div class="news-item">
				<div class="news-title">{news[currentIndex].title}</div>
				<div class="news-date">{news[currentIndex].date}</div>
			</div>
			<div class="news-indicator">
				{currentIndex + 1} / {news.length}
			</div>
		</div>
	{/if}
</div>

<style>
	.news {
		background: rgba(0, 0, 0, 0.3);
		padding: 1.5rem;
		border-radius: 1rem;
		backdrop-filter: blur(10px);
		color: white;
		min-height: 150px;
	}

	.news-container {
		display: flex;
		flex-direction: column;
		gap: 0.75rem;
	}

	.news-header {
		font-size: 1.5rem;
		font-weight: 600;
		color: #0088cc;
		text-transform: uppercase;
	}

	.news-item {
		flex: 1;
	}

	.news-title {
		font-size: 1.2rem;
		line-height: 1.4;
		margin-bottom: 0.5rem;
	}

	.news-date {
		font-size: 0.9rem;
		opacity: 0.7;
	}

	.news-indicator {
		font-size: 0.85rem;
		opacity: 0.6;
		text-align: right;
	}

	.loading,
	.error {
		text-align: center;
		padding: 1rem;
		opacity: 0.8;
	}
</style>
