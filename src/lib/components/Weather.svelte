<script lang="ts">
	import { onMount } from 'svelte';

	interface WeatherData {
		temperature: number;
		description: string;
		icon: string;
	}

	let weather = $state<WeatherData | null>(null);
	let loading = $state(true);
	let error = $state('');

	async function fetchWeather() {
		try {
			loading = true;
			error = '';
			
			// Using Open-Meteo API (no API key required)
			const response = await fetch(
				'https://api.open-meteo.com/v1/forecast?latitude=52.52&longitude=13.41&current=temperature_2m,weather_code&timezone=Europe/Berlin'
			);
			
			if (!response.ok) {
				throw new Error('Weather fetch failed');
			}
			
			const data = await response.json();
			
			const weatherCode = data.current.weather_code;
			const description = getWeatherDescription(weatherCode);
			const icon = getWeatherIcon(weatherCode);
			
			weather = {
				temperature: Math.round(data.current.temperature_2m),
				description,
				icon
			};
			
			loading = false;
		} catch (e) {
			error = 'Could not load weather';
			loading = false;
		}
	}

	function getWeatherDescription(code: number): string {
		const descriptions: Record<number, string> = {
			0: 'Klar',
			1: 'Überwiegend klar',
			2: 'Teilweise bewölkt',
			3: 'Bewölkt',
			45: 'Nebel',
			48: 'Nebel',
			51: 'Leichter Nieselregen',
			53: 'Nieselregen',
			55: 'Starker Nieselregen',
			61: 'Leichter Regen',
			63: 'Regen',
			65: 'Starker Regen',
			71: 'Leichter Schneefall',
			73: 'Schneefall',
			75: 'Starker Schneefall',
			80: 'Regenschauer',
			81: 'Regenschauer',
			82: 'Starke Regenschauer',
			95: 'Gewitter'
		};
		return descriptions[code] || 'Unbekannt';
	}

	function getWeatherIcon(code: number): string {
		if (code === 0 || code === 1) return '☀️';
		if (code === 2 || code === 3) return '☁️';
		if (code === 45 || code === 48) return '🌫️';
		if (code >= 51 && code <= 55) return '🌧️';
		if (code >= 61 && code <= 65) return '🌧️';
		if (code >= 71 && code <= 75) return '❄️';
		if (code >= 80 && code <= 82) return '🌦️';
		if (code === 95) return '⛈️';
		return '🌤️';
	}

	onMount(() => {
		fetchWeather();
		const interval = setInterval(fetchWeather, 600000); // Update every 10 minutes
		return () => clearInterval(interval);
	});
</script>

<div class="weather">
	{#if loading}
		<div class="loading">Loading weather...</div>
	{:else if error}
		<div class="error">{error}</div>
	{:else if weather}
		<div class="weather-content">
			<div class="weather-icon">{weather.icon}</div>
			<div class="weather-info">
				<div class="temperature">{weather.temperature}°C</div>
				<div class="description">{weather.description}</div>
				<div class="location">Berlin</div>
			</div>
		</div>
	{/if}
</div>

<style>
	.weather {
		background: rgba(0, 0, 0, 0.3);
		padding: 1.5rem;
		border-radius: 1rem;
		backdrop-filter: blur(10px);
		color: white;
	}

	.weather-content {
		display: flex;
		align-items: center;
		gap: 1rem;
	}

	.weather-icon {
		font-size: 4rem;
	}

	.weather-info {
		display: flex;
		flex-direction: column;
		gap: 0.25rem;
	}

	.temperature {
		font-size: 2.5rem;
		font-weight: 300;
	}

	.description {
		font-size: 1.2rem;
		opacity: 0.9;
	}

	.location {
		font-size: 1rem;
		opacity: 0.7;
	}

	.loading,
	.error {
		text-align: center;
		padding: 1rem;
		opacity: 0.8;
	}
</style>
