<script>
  import { animeData } from './data/animeData.js';
  import { goalsData } from './data/goalsData.js';

  import Navbar from './components/Navbar.svelte';
  import FilterBar from './components/FilterBar.svelte';
  import AnimeCard from './components/AnimeCard.svelte';
  import AnimeModal from './components/AnimeModal.svelte';
  import GoalsView from './components/GoalsView.svelte';

  import { Film } from 'lucide-svelte';

  // State
  let activeTab = $state('anime'); // 'anime' | 'goals'
  let searchQuery = $state('');
  let selectedStatus = $state('all');
  let selectedGenre = $state('all');
  let sortBy = $state('id');
  let viewMode = $state('grid'); // 'grid' | 'list'
  let selectedAnime = $state(null);

  // Extract all unique genres
  let genresList = $derived.by(() => {
    const set = new Set();
    for (const anime of animeData) {
      if (anime.genre && Array.isArray(anime.genre)) {
        for (const g of anime.genre) {
          if (g && g.trim()) set.add(g.trim());
        }
      }
    }
    return Array.from(set).sort();
  });

  // Calculate status counts across entire dataset
  let statusCounts = $derived.by(() => {
    let completed = 0;
    let ongoing = 0;
    let notWatched = 0;

    for (const a of animeData) {
      const s = (a.status || '').toLowerCase();
      if (s.includes('completed')) completed++;
      else if (s.includes('ongoing')) ongoing++;
      else if (s.includes('not watched')) notWatched++;
    }

    return {
      all: animeData.length,
      completed,
      ongoing,
      notWatched
    };
  });

  // Filter & Sort Anime
  let filteredAnime = $derived.by(() => {
    let result = [...animeData];

    // Status filter
    if (selectedStatus !== 'all') {
      result = result.filter(a => {
        const s = (a.status || '').toLowerCase();
        if (selectedStatus === 'Completed') return s.includes('completed');
        if (selectedStatus === 'Ongoing..') return s.includes('ongoing');
        if (selectedStatus === 'Not Watched') return s.includes('not watched');
        return a.status === selectedStatus;
      });
    }

    // Genre filter
    if (selectedGenre !== 'all') {
      result = result.filter(a => 
        a.genre && Array.isArray(a.genre) && a.genre.some(g => g.toLowerCase() === selectedGenre.toLowerCase())
      );
    }

    // Search query
    if (searchQuery.trim() !== '') {
      const q = searchQuery.toLowerCase().trim();
      result = result.filter(a => {
        const nameMatch = a.name.toLowerCase().includes(q);
        const genreMatch = a.genre && a.genre.some(g => g.toLowerCase().includes(q));
        const descMatch = (a.description || '').toLowerCase().includes(q);
        return nameMatch || genreMatch || descMatch;
      });
    }

    // Sorting
    result.sort((a, b) => {
      if (sortBy === 'name-asc') {
        return a.name.localeCompare(b.name);
      } else if (sortBy === 'name-desc') {
        return b.name.localeCompare(a.name);
      } else if (sortBy === 'score') {
        return (b.score || 0) - (a.score || 0);
      } else if (sortBy === 'year') {
        return (b.year || 0) - (a.year || 0);
      }
      // default: by id
      return a.id - b.id;
    });

    return result;
  });

  function handleTagClick(genre) {
    selectedGenre = genre;
    activeTab = 'anime';
    window.scrollTo({ top: 0, behavior: 'smooth' });
  }

  function resetFilters() {
    searchQuery = '';
    selectedStatus = 'all';
    selectedGenre = 'all';
    sortBy = 'id';
  }
</script>

<div class="app-shell">
  <!-- Top Navbar -->
  <Navbar 
    {activeTab} 
    onTabChange={(tab) => activeTab = tab}
    animeCount={animeData.length}
    goalsCount={goalsData.length}
  />

  <main class="main-content">
    {#if activeTab === 'anime'}
      <!-- Filter Controls & Search -->
      <FilterBar 
        {searchQuery}
        {selectedStatus}
        {selectedGenre}
        {sortBy}
        {viewMode}
        {genresList}
        {statusCounts}
        totalFiltered={filteredAnime.length}
        onSearchChange={(q) => searchQuery = q}
        onStatusChange={(s) => selectedStatus = s}
        onGenreChange={(g) => selectedGenre = g}
        onSortChange={(sb) => sortBy = sb}
        onViewModeChange={(vm) => viewMode = vm}
        onResetFilters={resetFilters}
      />

      <!-- Anime Cards Container -->
      <section class="cards-section">
        <div class="container">
          {#if filteredAnime.length > 0}
            <div class="cards-grid {viewMode === 'list' ? 'list-layout' : ''}">
              {#each filteredAnime as anime (anime.id)}
                <AnimeCard 
                  {anime} 
                  {viewMode}
                  onSelectAnime={(item) => selectedAnime = item}
                  onTagClick={handleTagClick}
                />
              {/each}
            </div>
          {:else}
            <!-- Empty State -->
            <div class="empty-state">
              <span class="empty-icon"><Film size={48} /></span>
              <h3>No Titles Match Your Filter</h3>
              <p>Try searching for a different keyword, status, or clear current filters.</p>
              <button class="clear-filters-btn" onclick={resetFilters}>
                Clear All Filters
              </button>
            </div>
          {/if}
        </div>
      </section>

    {:else if activeTab === 'goals'}
      <!-- Goals View -->
      <GoalsView initialGoals={goalsData} />
    {/if}
  </main>

  <!-- Detail & Journal Modal -->
  {#if selectedAnime}
    <AnimeModal 
      anime={selectedAnime} 
      onClose={() => selectedAnime = null}
      onTagClick={handleTagClick}
    />
  {/if}

  <!-- Footer -->
  <footer class="app-footer">
    <div class="container footer-content">
      <div class="footer-left">
        <span class="footer-logo">My Journel</span>
        <span class="footer-tagline">Personal Anime & Goals Archive</span>
      </div>

      <div class="footer-center">
        <span class="pill-badge">Static JSON Data</span>
        <span class="pill-badge">Svelte 5</span>
        <span class="pill-badge">SCSS</span>
      </div>

      <div class="footer-right">
        <span>{animeData.length} Anime • {goalsData.length} Goals Logged</span>
      </div>
    </div>
  </footer>
</div>

<style lang="scss">
  @use './styles/variables' as *;
  @use './styles/mixins' as *;

  .app-shell {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
    background-color: $bg-primary;
  }

  .main-content {
    flex: 1;
  }

  .cards-section {
    padding-bottom: 4rem;
  }

  .cards-grid {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 1.5rem;

    @media (max-width: 1400px) {
      grid-template-columns: repeat(4, 1fr);
      gap: 1.25rem;
    }

    @media (max-width: 1080px) {
      grid-template-columns: repeat(3, 1fr);
      gap: 1rem;
    }

    @media (max-width: 720px) {
      grid-template-columns: repeat(2, 1fr);
      gap: 0.85rem;
    }

    @media (max-width: 440px) {
      grid-template-columns: 1fr;
      gap: 1rem;
    }

    &.list-layout {
      display: flex;
      flex-direction: column;
      gap: 0.75rem;
    }
  }

  .empty-state {
    @include glass-panel(16px, rgba(16, 17, 24, 0.7), rgba(255, 255, 255, 0.06));
    border-radius: $radius-xl;
    padding: 3.5rem 1.5rem;
    text-align: center;
    @include flex-center;
    flex-direction: column;
    gap: 0.85rem;
    margin: 2rem 0;

    .empty-icon {
      color: $accent-red;
      opacity: 0.6;
      display: flex;
    }

    h3 {
      font-size: 1.25rem;
      font-weight: 700;
      color: #fff;
    }

    p {
      color: $text-secondary;
      font-size: 0.9rem;
      max-width: 400px;
    }

    .clear-filters-btn {
      margin-top: 0.5rem;
      padding: 0.6rem 1.25rem;
      border-radius: $radius-full;
      background: $accent-red;
      color: #fff;
      font-weight: 700;
      font-size: 0.85rem;
      box-shadow: 0 0 15px $accent-red-glow;
      transition: all $transition-fast;

      &:hover {
        box-shadow: 0 0 25px rgba(255, 51, 102, 0.5);
        transform: translateY(-2px);
      }
    }
  }

  .app-footer {
    border-top: 1px solid rgba(255, 255, 255, 0.06);
    background: #090a0d;
    padding: 1.5rem 0;
    margin-top: auto;

    .footer-content {
      @include flex-between;
      gap: 1rem;
      font-size: 0.82rem;
      color: $text-muted;

      @include mobile {
        flex-direction: column;
        text-align: center;
        gap: 0.75rem;
      }
    }

    .footer-left {
      display: flex;
      align-items: center;
      gap: 0.65rem;

      @include mobile {
        flex-direction: column;
        gap: 0.2rem;
      }

      .footer-logo {
        font-weight: 800;
        color: #fff;
        letter-spacing: 0.05em;
      }

      .footer-tagline {
        color: $text-dim;
      }
    }

    .footer-center {
      display: flex;
      align-items: center;
      gap: 0.5rem;

      .pill-badge {
        font-size: 0.7rem;
        font-family: $font-mono;
        padding: 0.15rem 0.5rem;
        border-radius: $radius-full;
        background: rgba(255, 255, 255, 0.04);
        border: 1px solid rgba(255, 255, 255, 0.08);
        color: $text-secondary;
      }
    }

    .footer-right {
      font-family: $font-mono;
      font-size: 0.75rem;
      color: $text-dim;
    }
  }
</style>
