# Wetter App (Vue 3 + OpenWeatherMap)

Kleine **Vue-3-Wetter-App** (Single File Component) mit dynamischem Hintergrundbild je nach Wetterlage und Tageszeit.

## Features

- Stadt-Suche (Enter oder Button)
- Anzeige von:
  - Beschreibung (DE)
  - Temperatur
  - „Fühlt sich an wie“
  - Min/Max Temperatur
- Dynamische Hintergrundbilder:
  - **Tageszeit**: Abendhimmel / Nachthimmel / Default
  - **Wetter**: Mapping von deutschsprachigen Beschreibungen → passende Bilder
- Fehlerbehandlung (z. B. Stadt nicht gefunden / API-Key ungültig)
- Übergangsanimation beim Hintergrundwechsel

---

## Tech Stack

- Vue 3 (Composition API, `<script setup>`)
- Axios
- OpenWeatherMap API

---

## Voraussetzungen

- Node.js + npm (oder pnpm/yarn)
- OpenWeatherMap API Key

---

## Installation & Start

> Beispiel für ein Vite-Vue-Projekt.

```bash
npm install
npm run dev
