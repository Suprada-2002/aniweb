<script>
  import { Star, Film, Eye, BookOpen } from 'lucide-svelte';

  let { 
    anime, 
    viewMode = 'grid', 
    onSelectAnime = () => {}, 
    onTagClick = () => {} 
  } = $props();

  let imgFailed = $state(false);

  function handleImageError() {
    imgFailed = true;
  }

  // Normalize status for styling
  let isCompleted = $derived(anime.status.toLowerCase().includes('completed'));
  let isOngoing = $derived(anime.status.toLowerCase().includes('ongoing'));
  let isNotWatched = $derived(anime.status.toLowerCase().includes('not watched'));

  let statusClass = $derived(
    isCompleted ? 'status-completed' : isOngoing ? 'status-ongoing' : 'status-not-watched'
  );

  let statusDisplay = $derived(
    isCompleted ? 'Completed' : isOngoing ? 'Ongoing' : 'Plan to Watch'
  );
</script>

{#if viewMode === 'grid'}
  <div 
    class="anime-card grid-card"
    onclick={() => onSelectAnime(anime)}
    role="button"
    tabindex="0"
    onkeydown={(e) => (e.key === 'Enter' || e.key === ' ') && onSelectAnime(anime)}
  >
    <!-- Poster Container -->
    <div class="poster-container">
      {#if anime.url && !imgFailed}
        <img 
          src={anime.url.trim()} 
          alt={anime.name} 
          loading="lazy"
          onerror={handleImageError}
          class="poster-img"
        />
      {:else}
        <div class="poster-fallback">
          <span class="fallback-icon"><Film size={36} /></span>
          <span class="fallback-title">{anime.name}</span>
          <span class="fallback-tag">Poster Unavailable</span>
        </div>
      {/if}

      <!-- Top Overlay Badges -->
      <div class="overlay-top">
        <span class="status-badge {statusClass}">
          <span class="pulse-dot"></span>
          {statusDisplay}
        </span>

        {#if anime.score}
          <div class="rating-badge">
            <span class="star-icon"><Star size={12} fill="#fbbf24" /></span>
            <span>{anime.score}</span>
          </div>
        {/if}
      </div>

      <!-- Quick Action Hover Overlay -->
      <div class="poster-hover-overlay">
        <div class="inspect-btn">
          <BookOpen size={16} />
          <span>Read Journal</span>
        </div>
      </div>
    </div>

    <!-- Card Content -->
    <div class="card-content">
      <div class="card-meta-top">
        <span class="type-chip">{anime.type || 'TV Series'}</span>
        {#if anime.year}
          <span class="year-chip">{anime.year}</span>
        {/if}
        <span class="id-chip">#{anime.id}</span>
      </div>

      <h3 class="anime-title" title={anime.name}>
        {anime.name}
      </h3>

      <!-- Tag List in Card -->
      <div class="card-tags">
        {#if anime.genre && anime.genre.length > 0}
          {#each anime.genre as tag}
            <button 
              class="genre-tag" 
              onclick={(e) => {
                e.stopPropagation();
                onTagClick(tag);
              }}
              title="Filter by {tag}"
            >
              #{tag}
            </button>
          {/each}
        {:else}
          <span class="no-tags">No tags assigned</span>
        {/if}
      </div>

      {#if anime.description}
        <p class="anime-desc">{anime.description}</p>
      {/if}
    </div>
  </div>

{:else}
  <!-- Horizontal List View Item -->
  <div 
    class="anime-card list-card"
    onclick={() => onSelectAnime(anime)}
    role="button"
    tabindex="0"
    onkeydown={(e) => (e.key === 'Enter' || e.key === ' ') && onSelectAnime(anime)}
  >
    <div class="list-thumb">
      {#if anime.url && !imgFailed}
        <img 
          src={anime.url.trim()} 
          alt={anime.name} 
          loading="lazy"
          onerror={handleImageError}
        />
      {:else}
        <div class="list-thumb-fallback">
          <Film size={20} />
        </div>
      {/if}
    </div>

    <div class="list-info">
      <div class="list-header">
        <div class="list-title-group">
          <span class="id-badge">#{anime.id}</span>
          <h3 class="list-title">{anime.name}</h3>
          <span class="type-pill">{anime.type || 'TV'}</span>
        </div>

        <div class="list-badges">
          {#if anime.score}
            <div class="rating-badge">
              <span class="star-icon"><Star size={12} fill="#fbbf24" /></span>
              <span>{anime.score}</span>
            </div>
          {/if}
          <span class="status-badge {statusClass}">
            {statusDisplay}
          </span>
        </div>
      </div>

      <div class="list-bottom">
        <div class="card-tags">
          {#if anime.genre && anime.genre.length > 0}
            {#each anime.genre as tag}
              <button 
                class="genre-tag" 
                onclick={(e) => {
                  e.stopPropagation();
                  onTagClick(tag);
                }}
              >
                #{tag}
              </button>
            {/each}
          {/if}
        </div>

        <button class="list-open-btn" onclick={(e) => { e.stopPropagation(); onSelectAnime(anime); }}>
          <Eye size={14} />
          <span>Details</span>
        </button>
      </div>
    </div>
  </div>
{/if}

<style lang="scss">
  @use '../styles/variables' as *;
  @use '../styles/mixins' as *;

  // Base Card
  .anime-card {
    @include glass-panel(16px, rgba(16, 17, 24, 0.75), rgba(255, 255, 255, 0.07));
    border-radius: $radius-lg;
    overflow: hidden;
    cursor: pointer;
    transition: all $transition-normal;
    position: relative;

    &:hover {
      transform: translateY(-4px);
      border-color: rgba(255, 51, 102, 0.45);
      box-shadow: 0 12px 30px rgba(0, 0, 0, 0.6), 0 0 20px rgba(255, 51, 102, 0.15);
      background: rgba(22, 24, 34, 0.9);
    }
  }

  // Grid Card Layout
  .grid-card {
    display: flex;
    flex-direction: column;
    height: 100%;

    .poster-container {
      position: relative;
      width: 100%;
      aspect-ratio: 1 / 1.42;
      background: #090a0d;
      overflow: hidden;

      .poster-img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        transition: transform 0.4s cubic-bezier(0.25, 1, 0.5, 1);
      }

      &:hover .poster-img {
        transform: scale(1.06);
      }

      .poster-hover-overlay {
        position: absolute;
        inset: 0;
        background: rgba(8, 9, 13, 0.7);
        backdrop-filter: blur(4px);
        @include flex-center;
        opacity: 0;
        transition: opacity $transition-normal;

        .inspect-btn {
          display: flex;
          align-items: center;
          gap: 0.5rem;
          padding: 0.55rem 1rem;
          background: $accent-red;
          color: #fff;
          border-radius: $radius-full;
          font-size: 0.82rem;
          font-weight: 700;
          box-shadow: 0 0 20px $accent-red-glow;
          transform: translateY(10px);
          transition: transform $transition-bounce;
        }
      }

      &:hover .poster-hover-overlay {
        opacity: 1;

        .inspect-btn {
          transform: translateY(0);
        }
      }

      .poster-fallback {
        width: 100%;
        height: 100%;
        @include flex-center;
        flex-direction: column;
        padding: 1.5rem;
        background: linear-gradient(145deg, #12131b 0%, #08090d 100%);
        color: $text-muted;
        text-align: center;
        gap: 0.65rem;

        .fallback-icon {
          color: $accent-red;
          opacity: 0.7;
          display: flex;
        }

        .fallback-title {
          font-size: 0.9rem;
          font-weight: 700;
          color: #fff;
          @include multi-line-clamp(2);
        }

        .fallback-tag {
          font-size: 0.7rem;
          font-family: $font-mono;
          color: $text-dim;
        }
      }
    }

    .overlay-top {
      position: absolute;
      top: 0.75rem;
      left: 0.75rem;
      right: 0.75rem;
      @include flex-between;
      z-index: 2;
      pointer-events: none;
    }

    .card-content {
      padding: 1rem;
      display: flex;
      flex-direction: column;
      flex: 1;
      gap: 0.65rem;
    }

    .card-meta-top {
      display: flex;
      align-items: center;
      gap: 0.45rem;
      font-size: 0.72rem;
      font-family: $font-mono;
      color: $text-muted;

      .type-chip {
        color: $accent-purple;
        font-weight: 600;
      }

      .year-chip {
        color: $text-secondary;
      }

      .id-chip {
        margin-left: auto;
        color: $text-dim;
      }
    }

    .anime-title {
      font-size: 1.05rem;
      font-weight: 700;
      line-height: 1.3;
      color: #fff;
      @include multi-line-clamp(2);
      transition: color $transition-fast;

      &:hover {
        color: $accent-red;
      }
    }

    .anime-desc {
      font-size: 0.8rem;
      color: $text-secondary;
      line-height: 1.45;
      @include multi-line-clamp(2);
      margin-top: auto;
    }
  }

  // Tags Inside Cards
  .card-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 0.4rem;

    .genre-tag {
      padding: 0.2rem 0.55rem;
      border-radius: $radius-sm;
      font-size: 0.72rem;
      font-weight: 600;
      background: rgba(255, 255, 255, 0.04);
      color: $text-secondary;
      border: 1px solid rgba(255, 255, 255, 0.08);
      transition: all $transition-fast;

      &:hover {
        background: rgba(255, 51, 102, 0.15);
        color: #fff;
        border-color: rgba(255, 51, 102, 0.4);
        transform: translateY(-1px);
      }
    }

    .no-tags {
      font-size: 0.72rem;
      color: $text-dim;
      font-style: italic;
    }
  }

  // Badges & Status Indicators
  .status-badge {
    display: inline-flex;
    align-items: center;
    gap: 0.4rem;
    padding: 0.25rem 0.6rem;
    border-radius: $radius-full;
    font-size: 0.7rem;
    font-weight: 700;
    backdrop-filter: blur(8px);
    letter-spacing: 0.04em;

    .pulse-dot {
      width: 6px;
      height: 6px;
      border-radius: 50%;
    }

    &.status-completed {
      background: rgba(16, 185, 129, 0.2);
      border: 1px solid rgba(16, 185, 129, 0.5);
      color: #34d399;

      .pulse-dot {
        background: #34d399;
        box-shadow: 0 0 6px #34d399;
      }
    }

    &.status-ongoing {
      background: rgba(245, 158, 11, 0.2);
      border: 1px solid rgba(245, 158, 11, 0.5);
      color: #fbbf24;

      .pulse-dot {
        background: #fbbf24;
        box-shadow: 0 0 6px #fbbf24;
      }
    }

    &.status-not-watched {
      background: rgba(148, 163, 184, 0.2);
      border: 1px solid rgba(148, 163, 184, 0.4);
      color: #cbd5e1;

      .pulse-dot {
        background: #94a3b8;
      }
    }
  }

  .rating-badge {
    display: inline-flex;
    align-items: center;
    gap: 0.25rem;
    padding: 0.25rem 0.55rem;
    border-radius: $radius-full;
    font-size: 0.72rem;
    font-weight: 700;
    background: rgba(15, 16, 22, 0.8);
    backdrop-filter: blur(8px);
    border: 1px solid rgba(255, 255, 255, 0.12);
    color: #f8fafc;
    font-family: $font-mono;

    .star-icon {
      color: #fbbf24;
      display: flex;
    }
  }

  // Horizontal List View Styles
  .list-card {
    display: flex;
    align-items: center;
    padding: 0.85rem;
    gap: 1.25rem;
    margin-bottom: 0.75rem;

    @include mobile {
      flex-direction: column;
      align-items: stretch;
      gap: 0.85rem;
    }

    .list-thumb {
      width: 70px;
      height: 95px;
      border-radius: $radius-md;
      overflow: hidden;
      flex-shrink: 0;
      background: #090a0d;

      @include mobile {
        width: 100%;
        height: 140px;
      }

      img {
        width: 100%;
        height: 100%;
        object-fit: cover;
      }

      .list-thumb-fallback {
        width: 100%;
        height: 100%;
        @include flex-center;
        background: rgba(255, 255, 255, 0.05);
        color: $text-muted;
      }
    }

    .list-info {
      flex: 1;
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
    }

    .list-header {
      @include flex-between;
      gap: 1rem;

      @include mobile {
        flex-direction: column;
        align-items: flex-start;
        gap: 0.4rem;
      }

      .list-title-group {
        display: flex;
        align-items: center;
        gap: 0.65rem;

        .id-badge {
          font-size: 0.75rem;
          font-family: $font-mono;
          color: $text-dim;
        }

        .list-title {
          font-size: 1.1rem;
          font-weight: 700;
          color: #fff;
        }

        .type-pill {
          font-size: 0.7rem;
          padding: 0.1rem 0.45rem;
          border-radius: $radius-sm;
          background: rgba(139, 92, 246, 0.15);
          color: #c084fc;
          font-family: $font-mono;
        }
      }

      .list-badges {
        display: flex;
        align-items: center;
        gap: 0.5rem;
      }
    }

    .list-bottom {
      @include flex-between;
      gap: 1rem;

      @include mobile {
        flex-direction: column;
        align-items: stretch;
      }

      .list-open-btn {
        display: inline-flex;
        align-items: center;
        gap: 0.4rem;
        font-size: 0.78rem;
        font-weight: 600;
        color: $accent-red;
        padding: 0.35rem 0.8rem;
        border-radius: $radius-full;
        background: rgba(255, 51, 102, 0.08);
        border: 1px solid rgba(255, 51, 102, 0.25);
        transition: all $transition-fast;
        margin-left: auto;

        &:hover {
          background: $accent-red;
          color: #fff;
        }
      }
    }
  }
</style>
