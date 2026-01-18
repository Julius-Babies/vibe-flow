# vibe-flow

A Svelte 5 web application that mimics the Amazon Alexa Echo Show home screen, designed to be used on an old tablet.

## Features

- **Microsoft Bing Picture of the Day** - Beautiful daily background images
- **Live Clock** - Real-time clock display in 24-hour format
- **Date Display** - Current date in German format
- **Weather Information** - Live weather data for Berlin using Open-Meteo API
- **Tagesschau News** - Latest German news headlines that rotate automatically
- **Static Site** - Deployed to GitHub Pages using SvelteKit's static adapter

## Developing

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```sh
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```sh
npm run build
```

You can preview the production build with `npm run preview`.

## Deployment

The app is automatically deployed to GitHub Pages when changes are pushed to the `main` branch. The deployment workflow is configured in `.github/workflows/deploy.yml`.

## Technologies

- **Svelte 5** - Modern reactive framework
- **SvelteKit** - Application framework with static adapter
- **TypeScript** - Type-safe development
- **Vite** - Fast build tool
- **Open-Meteo API** - Weather data (no API key required)
- **Bing Image API** - Daily background images
- **RSS2JSON** - Tagesschau news feed

## APIs Used

- **Bing Image of the Day**: `https://www.bing.com/HPImageArchive.aspx`
- **Open-Meteo Weather**: `https://api.open-meteo.com/v1/forecast` (Berlin coordinates)
- **Tagesschau RSS Feed**: Via RSS2JSON service

## License

MIT
