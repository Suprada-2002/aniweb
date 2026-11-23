# KAIRO // Anime & Goals Journal (廻路)

A lightweight, responsive personal journal and watchlist built with **Svelte 5**, **Vite**, and **SCSS** featuring a deep black aesthetic, glowing accents, glassmorphic cards, tags, and interactive milestones.

---

## ⚡ Features

- **Lightweight & High Performance**: Bundled with Vite and Svelte 5. Production bundle is minimal (~45 KB gzipped JS).
- **Deep Black Theme**: True dark theme (`#08080a`), glassmorphic panels, glowing crimson (`#ff3366`) and violet accents, and subtle borders.
- **Card-Based Visual Layout**:
  - Anime / movie cards with poster covers, fallback artwork placeholders, rating badges, and format chips (TV / Movie).
  - **Tags Inside Cards**: Every card displays genre tags (`#Action`, `#Romance`, `#Sports`, etc.). Clicking any tag instantly filters the view!
  - **Quick Details & Journal Modal**: Clicking any card opens a full modal displaying detailed synopsis, external AniList search link, and a personal journal log (allows saving your thoughts and episode progress locally).
- **Search, Filter & Sort**:
  - Live real-time search across titles, genres, and synopsis keywords.
  - Status filters: **All (48)**, **Completed**, **Ongoing**, **Not Watched**.
  - Dropdown selector for all unique genres.
  - Sorting: By Default ID, Title (A-Z / Z-A), Score, Release Year.
  - View switch: **Grid Mode** vs **Compact List Mode**.
- **🎯 Life Goals & Milestones Tracker**:
  - Includes your tracking data for: *Learn Rubik's Cube*, *Skateboarding*, *Splits*, *Learn Butterfly Knife*, *Learn Chess*, *Cartwheels*.
  - Interactive sub-level checkboxes that update progress bars and goal completion status in real time (persisted to `localStorage`).
- **📊 Analytics & Insights**:
  - Visual distribution of genres across your watch history.
  - Status ratio (Completed vs Ongoing vs Backlog).
  - Media format breakdown (TV Series vs Movies).
  - Top 5 Hall of Fame & an interactive **Surprise Pick** wheel to decide what to watch next!
- **100% Static Data**: Kept static in `src/data/animeData.js` and `src/data/goalsData.js`.

---

## 🛠️ Project Structure

```
aniweb/
├── index.html
├── package.json
├── svelte.config.js
├── vite.config.js
├── src/
│   ├── main.js
│   ├── App.svelte
│   ├── data/
│   │   ├── animeData.js       # All 48 tracked anime & movies
│   │   └── goalsData.js       # Tracked skills & milestones
│   ├── components/
│   │   ├── Navbar.svelte      # Header navigation & tab switcher
│   │   ├── HeroStats.svelte   # Top statistics overview & progress bar
│   │   ├── FilterBar.svelte   # Search, genre tags, status pills & sort
│   │   ├── AnimeCard.svelte   # Anime card with clickable tags & badges
│   │   ├── AnimeModal.svelte  # Full detail & journal reflections modal
│   │   ├── GoalsView.svelte   # Goals tracker with interactive checkboxes
│   │   └── StatsView.svelte   # Visual genre & status analytics
│   └── styles/
│       ├── _variables.scss    # Black theme palette & typography
│       ├── _mixins.scss       # Glassmorphism, breakpoints & helpers
│       └── main.scss          # Global reset & typography
```

---

## 🚀 Getting Started

### 1. Run Development Server
```bash
npm run dev
```
Open `http://localhost:5173` in your browser.

### 2. Build for Production
```bash
npm run build
```
Creates an optimized static bundle in `dist/`.

### 3. Preview Production Build
```bash
npm run preview
```

---

## 📝 Customizing Static Data

To add or modify anime or goals:
- Edit [src/data/animeData.js](file:///home/suprada/Documents/projects/aniweb/src/data/animeData.js)
- Edit [src/data/goalsData.js](file:///home/suprada/Documents/projects/aniweb/src/data/goalsData.js)
