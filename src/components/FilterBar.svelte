<script>
  import { Search, X, LayoutGrid, List, SlidersHorizontal, ArrowUpDown, Filter } from 'lucide-svelte';

  let {
    searchQuery = '',
    selectedStatus = 'all',
    selectedGenre = 'all',
    sortBy = 'id',
    viewMode = 'grid',
    genresList = [],
    statusCounts = { all: 0, completed: 0, ongoing: 0, notWatched: 0 },
    totalFiltered = 0,
    onSearchChange = () => {},
    onStatusChange = () => {},
    onGenreChange = () => {},
    onSortChange = () => {},
    onViewModeChange = () => {},
    onResetFilters = () => {}
  } = $props();

  let hasActiveFilters = $derived(
    searchQuery.trim() !== '' || selectedStatus !== 'all' || selectedGenre !== 'all' || sortBy !== 'id'
  );
</script>

<div class="filter-section">
  <div class="container">
    <!-- Top Filter Row: Search & Controls -->
    <div class="filter-bar-main">
      <!-- Search Input -->
      <div class="search-box">
        <span class="search-icon"><Search size={18} /></span>
        <input 
          type="text" 
          placeholder="Search by title, genre, keyword..." 
          value={searchQuery}
          oninput={(e) => onSearchChange(e.target.value)}
        />
        {#if searchQuery}
          <button class="clear-btn" onclick={() => onSearchChange('')} aria-label="Clear search">
            <X size={15} />
          </button>
        {/if}
      </div>

      <!-- Controls Right: Sort & View Toggle -->
      <div class="filter-controls-right">
        <!-- Sort Dropdown -->
        <div class="select-wrapper">
          <span class="select-icon"><ArrowUpDown size={15} /></span>
          <select 
            value={sortBy} 
            onchange={(e) => onSortChange(e.target.value)}
            aria-label="Sort by"
          >
            <option value="id">Default (ID)</option>
            <option value="name-asc">Title (A → Z)</option>
            <option value="name-desc">Title (Z → A)</option>
            <option value="score">Rating (Highest)</option>
            <option value="year">Release (Newest)</option>
          </select>
        </div>

        <!-- View Mode Switch -->
        <div class="view-switch">
          <button 
            class="view-btn {viewMode === 'grid' ? 'active' : ''}" 
            onclick={() => onViewModeChange('grid')}
            title="Grid View"
          >
            <LayoutGrid size={17} />
          </button>
          <button 
            class="view-btn {viewMode === 'list' ? 'active' : ''}" 
            onclick={() => onViewModeChange('list')}
            title="List View"
          >
            <List size={17} />
          </button>
        </div>
      </div>
    </div>

    <!-- Status Tabs & Genre Tags Row -->
    <div class="filter-sub-bar">
      <!-- Status Pills -->
      <div class="status-pills">
        <button 
          class="status-pill {selectedStatus === 'all' ? 'active' : ''}" 
          onclick={() => onStatusChange('all')}
        >
          All <span class="pill-count">{statusCounts.all}</span>
        </button>

        <button 
          class="status-pill completed-pill {selectedStatus === 'Completed' ? 'active' : ''}" 
          onclick={() => onStatusChange('Completed')}
        >
          <span class="status-indicator ind-completed"></span>
          Completed <span class="pill-count">{statusCounts.completed}</span>
        </button>

        <button 
          class="status-pill ongoing-pill {selectedStatus === 'Ongoing..' ? 'active' : ''}" 
          onclick={() => onStatusChange('Ongoing..')}
        >
          <span class="status-indicator ind-ongoing"></span>
          Ongoing <span class="pill-count">{statusCounts.ongoing}</span>
        </button>

        <button 
          class="status-pill backlog-pill {selectedStatus === 'Not Watched' ? 'active' : ''}" 
          onclick={() => onStatusChange('Not Watched')}
        >
          <span class="status-indicator ind-backlog"></span>
          Not Watched <span class="pill-count">{statusCounts.notWatched}</span>
        </button>
      </div>

      <!-- Genre Dropdown / Selector -->
      <div class="genre-selector-wrapper">
        <span class="genre-icon"><Filter size={14} /></span>
        <select 
          value={selectedGenre} 
          onchange={(e) => onGenreChange(e.target.value)}
          aria-label="Filter by genre"
        >
          <option value="all">All Genres ({genresList.length})</option>
          {#each genresList as g}
            <option value={g}>{g}</option>
          {/each}
        </select>
      </div>
    </div>

    <!-- Active Filter Summary & Reset Bar -->
    <div class="filter-info-bar">
      <div class="info-text">
        Showing <span class="highlight">{totalFiltered}</span> titles
        {#if selectedGenre !== 'all'}
          tagged with <span class="genre-chip">{selectedGenre}</span>
        {/if}
        {#if selectedStatus !== 'all'}
          in status <span class="status-chip">{selectedStatus}</span>
        {/if}
        {#if searchQuery}
          matching "<span class="search-term">{searchQuery}</span>"
        {/if}
      </div>

      {#if hasActiveFilters}
        <button class="reset-filters-btn" onclick={onResetFilters}>
          <X size={13} />
          <span>Reset All Filters</span>
        </button>
      {/if}
    </div>
  </div>
</div>

<style lang="scss">
  @use '../styles/variables' as *;
  @use '../styles/mixins' as *;

  .filter-section {
    padding: 1.75rem 0 1.25rem;
  }

  .filter-bar-main {
    @include flex-between;
    gap: 1rem;
    margin-bottom: 0.85rem;

    @include mobile {
      flex-direction: column;
      align-items: stretch;
      gap: 0.75rem;
    }
  }

  .search-box {
    position: relative;
    flex: 1;
    max-width: 580px;

    @include mobile {
      max-width: 100%;
    }

    .search-icon {
      position: absolute;
      left: 1rem;
      top: 50%;
      transform: translateY(-50%);
      color: $text-muted;
      pointer-events: none;
      display: flex;
      align-items: center;
    }

    input {
      width: 100%;
      padding: 0.75rem 2.6rem 0.75rem 2.75rem;
      background: rgba(255, 255, 255, 0.04);
      border: 1px solid rgba(255, 255, 255, 0.08);
      border-radius: $radius-lg;
      color: #fff;
      font-size: 0.92rem;
      transition: all $transition-normal;

      &::placeholder {
        color: $text-muted;
      }

      &:focus {
        background: rgba(255, 255, 255, 0.07);
        border-color: rgba(255, 51, 102, 0.5);
        box-shadow: 0 0 15px rgba(255, 51, 102, 0.2);
      }
    }

    .clear-btn {
      position: absolute;
      right: 0.85rem;
      top: 50%;
      transform: translateY(-50%);
      color: $text-muted;
      padding: 0.2rem;
      border-radius: 50%;
      @include flex-center;

      &:hover {
        color: #fff;
        background: rgba(255, 255, 255, 0.1);
      }
    }
  }

  .filter-controls-right {
    display: flex;
    align-items: center;
    gap: 0.75rem;

    @include mobile {
      justify-content: space-between;
    }
  }

  .select-wrapper, .genre-selector-wrapper {
    position: relative;
    display: flex;
    align-items: center;

    select {
      appearance: none;
      background: rgba(255, 255, 255, 0.04);
      border: 1px solid rgba(255, 255, 255, 0.08);
      border-radius: $radius-lg;
      color: $text-primary;
      padding: 0.75rem 2rem 0.75rem 2.4rem;
      font-size: 0.85rem;
      font-weight: 500;
      cursor: pointer;
      transition: all $transition-normal;
      background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 24 24' fill='none' stroke='%2394a3b8' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Cpath d='m6 9 6 6 6-6'/%3E%3C/svg%3E");
      background-repeat: no-repeat;
      background-position: right 0.75rem center;

      &:hover {
        background-color: rgba(255, 255, 255, 0.07);
        border-color: rgba(255, 255, 255, 0.15);
      }

      &:focus {
        border-color: $accent-red;
      }

      option {
        background: #111218;
        color: #fff;
      }
    }
  }

  .select-wrapper {
    .select-icon {
      position: absolute;
      left: 0.85rem;
      color: $text-muted;
      pointer-events: none;
      display: flex;
      align-items: center;
      z-index: 1;
    }
  }

  .genre-selector-wrapper {
    .genre-icon {
      position: absolute;
      left: 0.85rem;
      color: $text-muted;
      pointer-events: none;
      display: flex;
      align-items: center;
      z-index: 1;
    }
  }

  .view-switch {
    display: flex;
    align-items: center;
    background: rgba(255, 255, 255, 0.03);
    border: 1px solid rgba(255, 255, 255, 0.06);
    padding: 0.2rem;
    border-radius: $radius-lg;

    .view-btn {
      padding: 0.55rem 0.65rem;
      border-radius: $radius-md;
      color: $text-muted;
      @include flex-center;
      transition: all $transition-normal;

      &:hover {
        color: #fff;
      }

      &.active {
        color: #fff;
        background: rgba(255, 255, 255, 0.1);
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.4);
      }
    }
  }

  .filter-sub-bar {
    @include flex-between;
    gap: 1rem;
    padding-bottom: 0.75rem;
    border-bottom: 1px solid rgba(255, 255, 255, 0.05);

    @include mobile {
      flex-direction: column;
      align-items: stretch;
      gap: 0.75rem;
    }
  }

  .status-pills {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    overflow-x: auto;
    padding-bottom: 0.2rem;
    @include custom-scrollbar;

    .status-pill {
      display: inline-flex;
      align-items: center;
      gap: 0.45rem;
      padding: 0.45rem 0.85rem;
      border-radius: $radius-full;
      font-size: 0.82rem;
      font-weight: 600;
      color: $text-secondary;
      background: rgba(255, 255, 255, 0.03);
      border: 1px solid rgba(255, 255, 255, 0.06);
      white-space: nowrap;
      transition: all $transition-normal;

      &:hover {
        background: rgba(255, 255, 255, 0.07);
        color: #fff;
      }

      &.active {
        color: #fff;
        background: rgba(255, 255, 255, 0.12);
        border-color: rgba(255, 255, 255, 0.25);
      }

      &.completed-pill.active {
        background: rgba(16, 185, 129, 0.15);
        border-color: rgba(16, 185, 129, 0.4);
        color: #fff;
      }

      &.ongoing-pill.active {
        background: rgba(245, 158, 11, 0.15);
        border-color: rgba(245, 158, 11, 0.4);
        color: #fff;
      }

      &.backlog-pill.active {
        background: rgba(148, 163, 184, 0.15);
        border-color: rgba(148, 163, 184, 0.35);
        color: #fff;
      }

      .pill-count {
        font-size: 0.7rem;
        padding: 0.1rem 0.4rem;
        border-radius: $radius-full;
        background: rgba(255, 255, 255, 0.08);
        color: $text-muted;
        font-family: $font-mono;
      }

      .status-indicator {
        width: 7px;
        height: 7px;
        border-radius: 50%;

        &.ind-completed { background: $accent-emerald; box-shadow: 0 0 6px $accent-emerald; }
        &.ind-ongoing { background: $accent-amber; box-shadow: 0 0 6px $accent-amber; }
        &.ind-backlog { background: $text-muted; }
      }
    }
  }

  .filter-info-bar {
    @include flex-between;
    gap: 1rem;
    padding-top: 0.75rem;
    font-size: 0.8rem;
    color: $text-muted;

    @include mobile {
      flex-direction: column;
      align-items: flex-start;
      gap: 0.5rem;
    }

    .info-text {
      .highlight {
        color: #fff;
        font-weight: 700;
        font-family: $font-mono;
      }

      .genre-chip, .status-chip {
        display: inline-block;
        padding: 0.1rem 0.45rem;
        border-radius: $radius-sm;
        font-size: 0.75rem;
        font-weight: 600;
        margin: 0 0.2rem;
      }

      .genre-chip {
        background: rgba(139, 92, 246, 0.15);
        color: #c084fc;
        border: 1px solid rgba(139, 92, 246, 0.3);
      }

      .status-chip {
        background: rgba(255, 51, 102, 0.15);
        color: $accent-red;
        border: 1px solid rgba(255, 51, 102, 0.3);
      }

      .search-term {
        color: #fff;
        font-weight: 600;
      }
    }

    .reset-filters-btn {
      display: inline-flex;
      align-items: center;
      gap: 0.35rem;
      font-size: 0.75rem;
      color: $accent-red;
      background: rgba(255, 51, 102, 0.08);
      border: 1px solid rgba(255, 51, 102, 0.25);
      padding: 0.3rem 0.65rem;
      border-radius: $radius-full;
      transition: all $transition-fast;

      &:hover {
        background: rgba(255, 51, 102, 0.2);
        color: #fff;
      }
    }
  }
</style>
